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
- **Drift detection**: `.claude/agents/drift-detector.md` — runs in CI on every push

## License

[MPL 2.0](LICENSE)
