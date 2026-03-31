# AI instructions

This repository is an example of adopting the
[Anchored Development](docs/specs/SPEC-000-anchored-development.md)
framework. It demonstrates the minimal setup for a new project.

## Documentation — Anchored Development

This project follows [Anchored Development](docs/specs/SPEC-000-anchored-development.md).

- **Specs**: `docs/specs/` — behavioral expectations by domain
- **ADRs**: `docs/decisions/` — architectural reasoning and rejected alternatives
- **Skills**: `.claude/skills/` — spec and adr
- **Agents**: `.claude/agents/` — drift-detector
- **Drift detection**: `.claude/agents/drift-detector.md` — runs in CI on every push; invoke locally to verify artifact consistency before pushing
