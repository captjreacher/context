# MGRNZ Context

Shared context repository for the MGRNZ ecosystem. This repo provides the foundational documents, templates, and standards that agents and products use to coordinate.

## What is MGRNZ?

MGRNZ is the **core orchestration layer** for a multi-product ecosystem. Products integrate through MGRNZ via events rather than talking directly to each other.

## Repository Structure

```
/ecosystem/     # Core ecosystem context and routing guidance
/templates/     # Canonical templates for common patterns (Phase 2)
/blueprints/    # Enforcement blueprints and schemas (Phase 2)
/standards/     # Cross-cutting standards (Phase 3)
```

## Quick Start

**For agents**: Start with [`/ecosystem/router-prompt.md`](ecosystem/router-prompt.md) to understand how to route work.

**For humans**: Read [`/ecosystem/ecosystem-context.md`](ecosystem/ecosystem-context.md) for the architectural overview.

**For integrations**: Check [`/ecosystem/index.yaml`](ecosystem/index.yaml) for the machine-readable ecosystem registry.

## Current Phase

**Phase 1: Bootstrap** (current)
- Core ecosystem docs
- Reserved directory structure
- Governance foundations

See [`/ecosystem/index.yaml`](ecosystem/index.yaml) for the full roadmap.

## Contributing

Changes to this repository affect the entire ecosystem. Please open an issue or draft PR for discussion before making changes.

## License

MIT — see [LICENSE](LICENSE)
