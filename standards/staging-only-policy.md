# Staging Only Policy

## Scope

Applies to all new repositories and one-shot builds.

## Rule

Repositories may deploy only to staging environments.

## Allowed

- staging.maximisedai.com
- sandbox environments

## Not Allowed

- direct deployment to production domains
- bypassing Cockpit deployment control

## Rationale

- centralised control
- consistent deployment tracking
- reduced risk

## Enforcement

- workflow templates enforce staging-only deploys
- agent instructions prohibit production workflows
