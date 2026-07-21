---
name: api-contracts
description: >-
  Design and change shared HTTP APIs, events, webhooks, schemas, public errors,
  pagination, versioning, idempotency, and compatibility. Use when adding or
  modifying endpoints, OpenAPI/Zod/DTO contracts, webhooks, or contract tests.
  Skip private local functions and pure internal refactors with no consumer.
---

# API Contracts

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Design and change shared interfaces with explicit semantics, compatibility, security, and verification. Consumers must be able to implement against a stable, machine-readable contract.

## When to use

- adding or changing HTTP APIs, events, webhooks, messages, schemas, or public errors
- designing pagination, filters, sorting, idempotency, versioning, or deprecation
- compatibility with other services, frontends, mobile clients, or external partners matters

## Do not use

- the function/module is private with no independent consumer
- the contract is already fixed and the task is pure internal implementation
- database or security rules are the primary concern and the API surface is incidental → prefer those domain skills

## Relationship to error-handling

Public error shapes, locale rules for user-visible copy, and leak prevention live in `error-handling`. This skill owns the **contract**: status semantics, stable `code`s, Problem Details fields, and how errors appear in OpenAPI/examples/tests.

## Operating rules

1. Shared contracts need a versioned machine-readable source when the toolchain supports it (OpenAPI, AsyncAPI, JSON Schema, Zod schemas exported as source of truth, Nest DTOs mirrored in OpenAPI).
2. Pin schema/tool versions the repo already uses — do not invent a second contract stack.
3. HTTP methods, status codes, caching, and conditional requests follow RFC 9110 unless a legacy contract explicitly diverges.
4. New HTTP errors prefer RFC 9457 Problem Details with stable `type` / `code`. Match existing error shape if the API is not migrating.
5. Classify every change from the **consumer** perspective: additive, weakly compatible, or breaking.
6. Breaking changes need versioning, a compatibility window, consumer coordination, or an explicit recovery/rollback plan.
7. Large lists need cursor/offset pagination or an explicit bounded `limit`. Never return unbounded collections by default.
8. Mutating operations that clients may retry need documented idempotency (key scope, TTL, replay response).
9. Never retry-document operations that can duplicate side effects unless idempotency is guaranteed (payments, emails, webhooks).
10. Events/webhooks declare identity, type, version, timestamp, producer, schema, ordering expectations, and delivery/retry semantics (including replay protection).
11. Auth, permissions, and rate limits are part of the contract when they affect callers — document required headers/scopes and failure codes.
12. Examples, schemas, and handlers must not diverge. Contract tests cover positive and negative paths.
13. User-visible fields in error/response copy follow the app locale (see `error-handling`). Machine fields (`code`, ids, enum values) stay stable English `snake_case` / agreed casing.

## Compatibility classification

| Class | Meaning | Examples |
|-------|---------|----------|
| Additive | Old clients keep working | new optional field, new endpoint, new enum value only if clients ignore unknowns |
| Weakly compatible | Works if clients are tolerant | tighter validation that still accepts prior valid payloads |
| Breaking | Old clients can fail | remove/rename field, change type/meaning, new required field, status/code change, enum narrowing |

Do **not** call a change "additive" without checking codegen, validators, and clients that reject unknown fields or unknown enum values.

## Workflow

1. Identify consumers and the machine-readable contract source (or create one if greenfield).
2. Define method/event, auth, permissions, media type, request, response, errors, headers, limits, and observability (`request_id` / trace).
3. Classify compatibility; if breaking, choose versioning or migration plan before coding.
4. Specify idempotency, pagination, ordering, filtering, and versioning as applicable.
5. Add examples and deprecation/migration notes.
6. Verify: schema validation, positive + negative cases, auth failures, idempotent replay, error codes, and compatibility assumptions.

## Resource design defaults

- Nouns for resources; HTTP verbs for actions. Prefer `POST /orders/{id}/cancel` over inventing verbs as paths unless the domain already does.
- Stable resource ids in responses; do not expose internal DB surrogate details beyond the agreed id format.
- Prefer cursor pagination for large/unstable lists; offset only when the product already depends on page numbers.
- Filtering/sorting: document allowed fields and reject unknown operators rather than silently ignoring unsafe ones.
- Partial updates: document PATCH semantics (JSON Merge Patch vs explicit nullable fields) — pick one per API surface.

## Idempotency

For retryable `POST`/`PATCH`/`PUT` with side effects:

- Accept `Idempotency-Key` (or project-equivalent header).
- Document key scope (per user / per resource), retention, and conflict behavior (same key + different body → error).
- Replays return the original success response (or a documented equivalent), not a second side effect.

## Events & webhooks

Minimum envelope:

```json
{
  "id": "evt_123",
  "type": "order.created",
  "version": "1.0",
  "occurred_at": "2026-01-01T00:00:00Z",
  "producer": "orders",
  "data": {}
}
```

- Delivery: at-least-once unless documented otherwise → consumers must be idempotent on `id`.
- Webhooks: signature verification, timestamp tolerance, and safe retry/backoff. No secret in query strings.

## Examples

### Good

```http
POST /orders
Idempotency-Key: ord_123
Content-Type: application/json

HTTP/1.1 409 Conflict
Content-Type: application/problem+json

{
  "type": "https://errors.example.test/out-of-stock",
  "title": "Sin stock",
  "status": 409,
  "code": "out_of_stock",
  "request_id": "req_123"
}
```

```json
{
  "items": [],
  "next_cursor": null,
  "limit": 50
}
```

### Bad

```json
{ "error": "Something went wrong", "data": [/* all rows */] }
```

Unstable error, unbounded list, no idempotency/event semantics.

## Anti-patterns

- assuming "additive" without checking enums/codegen/strict validators
- parsing errors only via free-text `detail` / `message` with no stable `code`
- unbounded list endpoints
- undocumented breaking field renames
- webhook without signature / replay protection
- OpenAPI/examples/handlers out of sync
- documenting retry on non-idempotent payments/emails/webhooks

## Done criteria

- [ ] Consumers can implement against a machine-readable (or explicitly documented) contract
- [ ] Compatibility class is stated; breaking risk has a plan
- [ ] Errors expose stable safe `code`s; no internal leaks
- [ ] Pagination/limits and idempotency documented where relevant
- [ ] Events/webhooks have identity, version, and delivery semantics
- [ ] Contract checks cover positive and negative paths (schema and/or tests)

## Framework adapters

Apply only what the repo uses. Contract rules above win over framework habits.

### NestJS

- DTOs + `class-validator` / `ValidationPipe` at the boundary; keep OpenAPI (`@nestjs/swagger`) aligned with DTOs
- Versioning via URI or header only if the project already standardized one — do not mix strategies
- Map domain errors in a global filter to the agreed error contract (`error-handling`)

### Express

- Validate with the project's schema lib (Zod/Joi/etc.) at the route boundary; export or mirror schemas for clients when applicable
- One response mapper for success/error envelopes; do not invent per-route shapes
- Prefer central OpenAPI/Zod source over hand-written stale docs

### Next.js (Route Handlers / Server Actions)

- Treat Route Handlers as public HTTP contracts when called by browsers/mobile/other services
- Server Actions used across trust boundaries still need explicit input validation and stable error codes
- Do not leak server exception messages to the client; map to contract codes

### React / SPA consumers

- Depend on stable `code` + typed client (OpenAPI codegen or shared Zod), not on English/Spanish title strings
- Handle unknown future enum/code values safely (fallback UI) when the API is additive
