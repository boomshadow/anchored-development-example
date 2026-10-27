---
title: "CI Drift Detector Runs with a Fixed, Read-Only Tool Set"
description: "The CI drift detector agent is given exactly four tools (Read, Glob, Grep, Bash), only git diff, git log and git show are pre-approved, and every other action is denied without asking. An auto-approve list alone was rejected because it leaves 24 tools available, including file writes and web access; a deny list was rejected because it still leaves 19 tools and does not cover tools added in later SDK versions; removing curl from the image was rejected because node, git and bash can all reach the network."
status: accepted
tags: [ci, drift-detection, security, least-privilege, agent-sdk, permissions, prompt-injection]
---

# ADR-001: CI Drift Detector Runs with a Fixed, Read-Only Tool Set

## Context

The drift detector (see SPEC-000, "Drift Detection") runs as the `anchor-watch` CI job on every push. It is an AI agent that reads untrusted text: the merge request diff, and any repository file it chooses to open. Anyone who can open a merge request controls part of that text, so it can carry instructions aimed at the agent.

The job runs with secrets in its environment — the Claude API token and the GitLab CI job token. An agent that can be steered by injected text and can also write files, run arbitrary commands, or reach the network is a path for those secrets to leave the job, or for the job to tamper with the repository.

The detector is a detector, never a fixer. It reports drift; it does not resolve it. It never needs to write anything, and it needs only enough shell to inspect git history.

## Decision

The CI drift detector runs with a fixed, read-only tool set:

- **Only four tools exist** for the agent: Read, Glob, Grep and Bash. The tool set is declared explicitly, so no other tool is loaded at all.
- **Only read-only git commands are pre-approved**: `git diff`, `git log` and `git show`. Reads inside the working directory need no approval; Read, Glob and Grep are deliberately not pre-approved by bare name, because a bare name approves the tool for any path on disk.
- **Anything else is denied without asking.** The permission mode never prompts and never escalates; an unapproved action simply fails.

The prompt also tells the agent that everything from the repository — the diff, file contents and git output — is data to analyze, never instructions to follow, and the diff is delimited so its content cannot close the block it sits in. This lowers the odds of a successful injection; the tool set is what bounds the damage if one succeeds.

### Rejected alternatives

**An auto-approve list alone.** Listing tools as pre-approved does not restrict which tools exist; it only skips the prompt for the listed ones. Measured offline against the Agent SDK in the CI image, a configuration with only an auto-approve list exposes 24 tools, including file writes and web access. Combined with a permission mode that accepts edits, it leaves the agent able to modify the checkout.

**A deny list.** Denying specific tools is subtractive: measured the same way, it still leaves 19 tools available. It also fails open over time — any tool added in a later SDK version is available until someone notices and adds it to the list. An allow list of the four tools fails closed.

**Removing curl from the image as the network control.** Network egress does not depend on curl. Node, git and bash can each open a connection on their own, so stripping one binary removes no capability and gives a false sense of containment.

## Consequences

- The agent cannot write files, edit the repository, or reach the network through its tools. Prompt injection in a diff can, at worst, bias the findings it reports.
- The residual channel is narrow, not zero. Read-only shell commands can still read environment variables, and findings are printed to the job log, so injected text could in principle steer a secret into a finding. Keeping the job log restricted to project members and keeping CI secrets masked remain relevant.
- `git diff`, `git log` and `git show` accept `--output=<file>`, so the pre-approved set is close to read-only, not strictly read-only: it can write a file with git output as its content.
- Extending the detector's capabilities is a deliberate act. Any new tool or command must be added to the explicit tool set or approval list, and this ADR updated with the reasoning.
- The constraints live in the CI invocation script, not in the agent's instructions file, so projects that customize the detector's instructions keep the same tool boundary.
