# Deployment Airway — Staging Model (V1)

## Purpose

Define a global deployment model for new builds and one-shot applications.

## Core Principle

Deployments are event-driven. Repositories do not control production.

## Allowed Flow

```text
Local Dev → Git Push → GitHub Actions → Staging (central) → Cockpit tracking
```

## Staging Target

```text
https://staging.maximisedai.com/<app-slug>/
```

## Rules

- All new repos deploy to staging only
- No direct production deployment workflows
- Deployment must be observable in Cockpit
- Deployments must emit events

## Responsibilities

### Repo
- contains app code
- contains staging deploy workflow

### GitHub Actions
- builds app
- deploys to staging path
- emits deployment events

### Staging Host
- central deployment surface
- owns domains and routing

### Cockpit
- tracks deployments
- manages approval and promotion

## Future

- production promotion via Cockpit
- multi-domain deployment
- deployment approvals
- rollback support
