---
name: create-use-case
description: >-
  Add a single backend use-case/application operation file with clear input,
  output, domain errors, and tests. Use when implementing an endpoint or job
  action. Skip pure DTO-only or frontend work. Template — adapt language and DI.
---

# Create Use Case

## Template

Copy into the target repo, then resolve every `ADAPT:`. Skeleton is framework-agnostic; wire DI the Nest/Express/project way.

## Purpose

Implement one material operation in its own file, framework-agnostic at the core.

## When to use

- new command/query behind an HTTP endpoint or worker
- extracting logic out of a fat controller/service

## Do not use

- transport-only changes (route path rename)
- schema-only OpenAPI edits → `api-contracts`

## Workflow

1. Name: verb + noun (`create-order`, `get-entity`)
2. Define input type + output type
3. Inject ports (repo, clock, id) — no `req`/`res` / framework Request types
4. Throw typed domain errors (`error-handling`)
5. Wire controller/route to call the use case
6. Unit test with fakes; HTTP test only for mapping

## Skeleton (ADAPT TS/JS)

```ts
export type CreateOrderInput = { /* ... */ };
export type CreateOrderResult = { id: string };

export async function createOrder(
  input: CreateOrderInput,
  deps: { orders: OrderRepo },
): Promise<CreateOrderResult> {
  // validate invariants, persist, return
}
```

## Checklist

- [ ] No HTTP types inside use case
- [ ] Domain errors for expected failures
- [ ] Idempotency considered for retries (`api-contracts`)
- [ ] AuthZ of concrete resource done before or inside with explicit actor (`security-engineering`)
