---
name: error-handling
description: >-
  Design and implement typed domain errors, API error contracts, boundary
  mapping, retries, observability, and user-facing error UX. Use when adding
  error types, exception filters/middleware, Problem Details, client error
  states, or fixing swallowed/leaky errors. Applies across NestJS, Express,
  Prisma, Drizzle, Sequelize/TypeORM, Next.js App Router, and React SPA.
  Skip trivial edits with no error surface.
---

# Error Handling

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Make failures detectable, diagnosable, and contractual — without leaking internals or breaking UX.

## When to use

- designing domain error types or exception hierarchies
- adding/changing API error responses or client error mapping
- implementing exception filters, error middleware, or equivalent boundaries
- adding retry/timeout/circuit breaker on external deps
- wiring UI loading/empty/error states or error boundaries
- fixing swallowed catches, ORM/driver leaks, or generic-500-everything

## Do not use

- change has no real error surface
- an adequate boundary already exists and this edit does not touch it

## Locale (user-facing copy)

- User-visible copy must match the application's active locale.
- Never mix languages within the same response.
- Machine-readable fields (codes, identifiers) remain stable and English.
- Developer logs remain English unless the project already uses another language.
- Match the existing i18n system when present — never hardcode a second language ad hoc.

## Operating rules

1. Fail fast and visible. Every `catch` handles, rethrows, or logs with context — never swallow.
2. Domain errors are typed values with stable `code` — not loose strings or ORM/driver messages.
3. Expected/business → client contract (4xx). Unexpected/infra → correlate + generic 5xx; never leak internals.
4. Map error → HTTP/response **once** at the presentation boundary. Use cases do not set status codes or framework HTTP exceptions.
5. Never expose ORM/driver/SDK messages, stacks, SQL, secrets, or PII to clients.
6. User message ≠ log message. Log `request_id`, operation, domain `code`, cause chain server-side.
7. Every unexpected error should be traceable through logs or telemetry (e.g. Sentry, OpenTelemetry, Datadog — use whatever the project already has; do not invent a stack).
8. Retry only with backoff + jitter + cap. Timeouts on external calls. Circuit breaker only when cascade risk is real. Never retry operations that may produce duplicate side effects unless idempotency is guaranteed (payments, emails, webhooks, etc.).
9. Public error shapes are part of the API contract — document and test them.

## HTTP contract

Prefer RFC 9457 Problem Details when greenfield or renegotiating:

```json
{
  "type": "https://errors.example.test/email-already-registered",
  "title": "Este email ya está registrado",
  "status": 409,
  "code": "email_already_registered",
  "request_id": "req_123",
  "errors": [{ "field": "email", "message": "Ya existe una cuenta con este email" }]
}
```

*(Example assumes a Spanish locale; `title` / `message` follow the app locale, `code` stays English.)*

If the repo already uses `{ error, code, ... }`, match that shape unless migrating the contract. Field validation → structured field errors, not a single opaque string.

## Boundaries

| Layer | Responsibility |
|-------|----------------|
| Domain / use case | Throw/return domain errors only |
| Infra / repo | Translate driver/SDK errors before leaving the adapter |
| Presentation | Single mapper: status + stable body + locale-aware user copy |

## Workflow

1. Identify the error boundary and the operation.
2. Reuse or define domain errors (`code`, category, status mapping at edge).
3. Map once to the stable API/UI contract; write user-facing strings in the app locale.
4. Separate user copy from developer log context (`request_id`, cause); ensure unexpected errors are traceable in logs/telemetry.
5. Add retry/timeout/breaker only where justified and idempotent (no duplicate side effects).
6. Document/test the codes touched; assert no internal leak to the client.

## Anti-patterns

- empty `catch` or catch + `console.log` only
- HTTP mapping inside use cases or repositories
- one blanket 500 for business conflicts
- retrying non-idempotent side effects (charges, emails, webhooks)
- ORM/driver unique-constraint text returned to clients
- mixed-language user-facing copy in the same response
- unexpected errors with no log/telemetry trail

## Done criteria

- [ ] Business vs unexpected errors distinguished
- [ ] Mapping centralized at the boundary
- [ ] No internal/ORM/PII leak to client (test when touching the mapper)
- [ ] User-visible messages match the app locale; no mixed languages
- [ ] Unexpected errors are traceable via logs or telemetry
- [ ] Retries/breakers only where idempotency is guaranteed; clear limits
- [ ] New/changed codes documented or covered by contract/UI mapping tests

## Framework adapters

Concrete defaults for common stacks. Apply only what the repo uses.

### NestJS

- Domain error classes with stable `code` (optional status hint; mapping still in filter)
- Global `ExceptionFilter` + `ValidationPipe`
- Prefer domain errors over scattering `HttpException` in services
- ORM failures → translate in infra or the filter, not in use cases

### Express

- Controllers: `next(err)`; one central error middleware
- Domain errors carry `code` + `statusCode`; middleware builds the response body
- Never `res.status(500).json({ error: e.message })` on ORM/driver failures

### Prisma / Drizzle / Sequelize / TypeORM

- Catch ORM errors in the repository/adapter; map to domain errors before crossing into use cases
- **Prisma**: handle `PrismaClientKnownRequestError` by `code` (e.g. `P2002` unique → conflict; `P2025` not found → not-found). Never forward `meta`, raw query, or Prisma message to the client
- **Drizzle**: treat driver/`DatabaseError` (e.g. Postgres `23505` unique_violation, `23503` FK) in the adapter; map SQLSTATE → domain error. Never return `cause`, query text, or driver message to the client
- **Sequelize / TypeORM**: same rule — translate constraint/driver errors in the adapter; never leak ORM messages
- Unique/FK/check violations are expected → 409/400 with stable `code` + locale-correct user copy; connection/timeout/unknown → 5xx + log/telemetry

### Next.js App Router

- `error.js` / `global-error.js` for render failures (locale-correct copy)
- Route Handlers / Server Actions: map known API `code` → UI; unknown → generic copy in the app locale
- Never dump server stacks or raw fetch bodies into the client
- Prefer typed failures at feature `api/` boundary over ad-hoc try/catch soup

### React SPA / client

- Feature states: loading | empty | error | success
- Map API `code` → dictionary/user copy; fallback generic + retry when safe
- Error boundaries for render crashes; form fields from `errors[]`
- No silent `.catch(() => {})`
