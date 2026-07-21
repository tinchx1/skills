---
name: backend-module-structure
description: >-
  Layout backend capabilities as modules with thin HTTP entrypoints and
  use-case files for NestJS or Express. Use when scaffolding or restructuring
  APIs. Skip frontend-only work; defer errors/contracts to global skills.
  Template — pick Nest or Express layout.
---

# Backend Module Structure

## Template

Copy into the target repo, then resolve every `ADAPT:`. Pick Nest **or** Express (or map to the project's framework). Defaults below are starter opinions, not project law.

## Purpose

Organize backend code by **capability**, not by technical type dumping grounds.

## When to use

- new Nest module or Express capability folder
- greenfield API layout
- stopping “controllers with business logic”

## Do not use

- frontend / Screaming Architecture work
- designing public OpenAPI alone → `api-contracts`
- error filter details → `error-handling`

## ADAPT: NestJS vs Express

### NestJS

```text
src/<capability>/
  <capability>.module.ts
  <capability>.controller.ts      # thin
  application/ or use-cases/      # one file per material operation
  domain/                         # optional: entities, domain errors
  infrastructure/                 # ORM/adapters
  dto/
```

### Express

```text
src/modules/<capability>/
  <capability>.routes.ts
  <capability>.controller.ts
  create-<thing>.use-case.ts
  <thing>.repository.ts
  schemas/                        # zod/joi/…
```

## Rules

1. Controllers/routes do not own business rules
2. One material operation → one use-case file
3. ORM/clients stay in infrastructure/adapters
4. Use cases throw domain errors; HTTP mapping once at the edge (`error-handling`)
5. Validate at the boundary (`api-contracts`)

## Done criteria

- [ ] Capability folder exists with thin entrypoint
- [ ] Use cases testable without HTTP
- [ ] No cross-capability deep coupling without an explicit port
