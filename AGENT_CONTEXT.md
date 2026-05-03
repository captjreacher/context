# Agent Context — MGRNZ Ecosystem

This repository is the shared context source for agents working across the MGRNZ ecosystem.

## Primary Rule

Do not guess ecosystem architecture. Read this repository first, then inspect the target product repository.

## Current Ecosystem Model

- MGRNZ is the core context, content, advisory IP, and event coordination layer.
- Paperclip is the operational orchestration layer and Cockpit control plane.
- MAXAI / Maximised AI is an AI-facing brand and demo surface.
- EngageGroovy is the agency, intake, content, and marketing execution channel.
- FunkMyBrand / PlayerCard is the CV deployment/product surface.

Products should remain independent. Shared behaviour should be expressed through event contracts, templates, and deployment policies in this context repo.

## Repo Reading Order for Agents

1. README.md
2. ecosystem/ecosystem-context.md
3. ecosystem/router-prompt.md
4. ecosystem/deployment-airway.md
5. standards/repo-boundaries.md
6. standards/staging-only-policy.md
7. blueprints/events/deployment-events-v1.md
8. templates/github-actions/staging-static-deploy.yml

## Deployment Rule

New builds and one-shot apps may deploy to staging only:

```text
https://staging.maximisedai.com/<app-slug>/
```

They must not deploy to production domains unless a separate Cockpit-controlled production promotion process exists.

## Agent Behaviour

Agents may create app-specific staging workflows using the canonical template, add deployment manifests to new repos, emit or document deployment events, and improve this context repo when gaps are found.

Agents must not add production deploy workflows to one-shot/new-build repos, hard-code live domains as deploy targets, bypass Cockpit/Paperclip deployment control, mix MGRNZ content architecture into unrelated product repos, or move existing live-site deployment models unless explicitly instructed.

## When to Escalate

Escalate before changing production deployment flow, DNS or domain ownership assumptions, event contract names, environment setup conventions, repo boundaries, or live production paths.
