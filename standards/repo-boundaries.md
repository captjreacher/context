# Repository Boundaries

Each repository owns its own logic and deployment. Shared behaviour must come from this context repo.

## Allowed

- use shared templates
- emit events

## Not Allowed

- embed core orchestration logic
- hard-code dependencies between products
