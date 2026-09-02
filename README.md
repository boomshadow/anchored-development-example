# Anchored Development — Example Repository

This repository demonstrates adopting
[Anchored Development](docs/specs/SPEC-000-anchored-development.md) in a
new project. It contains the minimum required setup: the framework spec,
spec and ADR tooling, drift detection, and CI validation.

## Documentation — Anchored Development

This project follows [Anchored Development](docs/specs/SPEC-000-anchored-development.md).

- **Specs**: `docs/specs/` — behavioral expectations by domain
- **ADRs**: `docs/decisions/` — architectural reasoning and rejected alternatives
- **Skills**: `.claude/skills/` — spec and adr
- **Agents**: `.claude/agents/` — drift-detector
- **Drift detection**: `.claude/agents/drift-detector.md` — runs in CI on every push (see [Drift detection in CI](#drift-detection-in-ci))

## Drift detection in CI

The framework requires that drift detection run on every change — it does not
prescribe *how*. This repo showcases one way to satisfy that: a GitLab CI job
named `anchor-watch` (see [`.gitlab-ci.yml`](.gitlab-ci.yml)) that runs a
pre-built image built for the purpose:
[anchor-watch-image](https://gitlab.com/boomshadow-public/anchor-watch-image).
Use it as-is, or adapt the approach to your own tooling — Codex, a script,
whatever satisfies the spec.

That image is Claude-based, so the only setup is a single CI environment
variable, `CLAUDE_CODE_OAUTH_TOKEN`, which reuses your existing Anthropic
subscription. Generate the token with `claude setup-token`
([docs](https://code.claude.com/docs/en/authentication#generate-a-long-lived-token)).

## License

[MPL 2.0](LICENSE)
