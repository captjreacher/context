# MGRNZ Ecosystem Context

## Overview

MGRNZ is the **core orchestration layer** for a multi-product ecosystem. Products don't talk to each other directly — they integrate through MGRNZ via a unified event system.

## Architecture Principles

### Event-Driven Integration
- Products emit events to MGRNZ
- MGRNZ routes, transforms, and dispatches events to subscribers
- Products remain decoupled — they know MGRNZ, not each other

### Core vs. Products
- **MGRNZ (core)**: Event bus, routing logic, shared context, standards enforcement
- **Products**: Independent applications that integrate via MGRNZ events

### Agent Coordination
- Agents operating within the ecosystem should understand they're part of a larger whole
- Cross-product work routes through MGRNZ context
- This repository provides the shared context agents need

## Event Flow

```
┌─────────────┐     events      ┌─────────┐     events      ┌─────────────┐
│  Product A  │ ───────────────▶│  MGRNZ  │───────────────▶ │  Product B  │
└─────────────┘                 │  (core) │                 └─────────────┘
                                └─────────┘
                                     │
                                     ▼
                              ┌─────────────┐
                              │  Product C  │
                              └─────────────┘
```

## What This Repository Contains

- `/ecosystem/` — Core context documents (this file, index, router guidance)
- `/templates/` — Canonical templates for common patterns (future)
- `/blueprints/` — Enforcement blueprints and schemas (future)
- `/standards/` — Cross-cutting standards and conventions (future)

## Governance

Changes to ecosystem context should be reviewed carefully — they affect all products and agents operating in the ecosystem.
