---
name: http-boundary
description: >-
  Keep Nest/Express HTTP handlers thin: validate input, call use case, map
  success/error once. Use when adding controllers, routes, or pipes/filters.
  Skip domain logic refactors with no transport change. Template — adapt
  framework idioms.
---

# HTTP Boundary

## Template

Copy into the target repo, then resolve every `ADAPT:`. Sketch is illustrative — use Nest pipes/filters or Express middleware as the project does.

## Purpose

Presentation layer translates transport ↔ application. It does not own business rules.

## When to use

- new/updated controller, route, pipe, middleware, exception filter
- fixing fat handlers

## Do not use

- implementing domain rules → `create-use-case`
- full OpenAPI design → `api-contracts`

## Rules

1. Validate body/params/query at the edge (DTO / schema)
2. Call one use case (or a thin orchestrator)
3. Map domain errors in **one** filter/middleware (`error-handling`)
4. Never `catch` and return `e.message` from ORM/driver
5. AuthN middleware + AuthZ on resource id (`security-engineering`)
6. Pagination/idempotency headers per `api-contracts`

## Thin handler sketch

```ts
// ADAPT: Express-style; Nest = controller method + pipe + filter
async function create(req, res, next) {
  try {
    const input = schema.parse(req.body);
    const result = await createOrder(input, deps);
    res.status(201).json(result);
  } catch (err) {
    next(err);
  }
}
```

## Checklist

- [ ] No business branching beyond mapping
- [ ] Stable success/error shapes
- [ ] Tests for mapping + validation failures
