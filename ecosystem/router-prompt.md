# Router Prompt — Agent Guidance

This document helps agents understand how to route work within the MGRNZ ecosystem.

## When You Receive a Request

### 1. Identify Scope

**Single-product work**: The request affects only one product.
- Handle within that product's context
- No need to involve MGRNZ core

**Cross-product work**: The request spans multiple products or affects the ecosystem.
- Route through MGRNZ context
- Consult this repository for shared standards
- Consider event implications

**Core work**: The request affects MGRNZ itself (event routing, shared context, standards).
- Work in this repository
- Changes require careful review

### 2. Check for Existing Patterns

Before implementing something new:
1. Check `/templates/` for canonical patterns
2. Check `/blueprints/` for schemas and constraints
3. Check `/standards/` for conventions

### 3. Event Awareness

If your work will:
- **Emit new events**: Register in `index.yaml`, define schema in `/blueprints/`
- **Subscribe to events**: Check `index.yaml` for available event types
- **Modify event flow**: Coordinate with affected products

## Routing Decision Tree

```
Is this single-product work?
├── YES → Handle in product context
└── NO → Does it affect event flow?
    ├── YES → Consult index.yaml, coordinate
    └── NO → Does it set ecosystem-wide precedent?
        ├── YES → Propose in /standards/
        └── NO → Handle pragmatically, document
```

## When to Escalate

Escalate to human review when:
- Adding a new product to the ecosystem
- Changing core event routing logic
- Establishing new ecosystem-wide standards
- Breaking changes to existing event contracts
