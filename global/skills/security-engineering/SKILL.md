---
name: security-engineering
description: >-
  Apply authn/authz, trust boundaries, secrets, privacy, input safety, and
  supply-chain hygiene while designing or implementing features. Use when
  touching login, permissions, tenancy, uploads, SSRF, crypto, sensitive data,
  or new dependencies. For /review-security or PR/diff security audits, use
  Cursor's built-in review-security skill instead. Skip harmless local refactors.
---

# Security Engineering

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Build security and privacy into the change with risk-based controls and evidence — not a post-hoc audit.

## Split with Cursor built-in

| Need | Use |
|------|-----|
| Implement/design secure behavior in this task | **this skill** |
| Audit branch/PR/uncommitted diff (`/review-security`) | built-in **`review-security`** → `security-review` subagent |
| Fix findings after that audit | implement here; re-run built-in review only if the user asks |

Do **not** launch the security-review subagent from this skill unless the user explicitly asked for a diff/PR security review.

## When to use

- authn/authz, privilege, tenancy, admin, or trust boundaries change
- sensitive data, privacy, secrets, crypto, uploads, parsers, SSRF, or dynamic execution
- new productive dependencies, build/CI secrets, or public/admin surfaces

## Do not use

- isolated non-security change with no data/access impact
- documentation-only with no sensitive content
- user only wants a diff audit → `review-security`

## Operating rules

1. Classify data and sensitive operations before coding.
2. Define trust boundaries and actors; “internal” is not automatically trusted.
3. Authorize every server-side operation on the **concrete resource** (id + tenant/owner), not only a coarse role.
4. Deny by default; least privilege for tokens, DB roles, and cloud permissions.
5. Treat all boundary-crossing input as untrusted; validate type and semantics at the edge.
6. Never concatenate untrusted input into queries, shell, or executable templates (ORM/query builder/parameter binding).
7. Never commit secrets or put them in prompts, tests, fixtures, logs, crash dumps, docs, or images. Use env/secret manager; rotate if exposed.
8. UI/hide buttons are not authorization — enforce on the server.
9. Log safe identifiers and outcomes only; no tokens, passwords, full PII payloads (align with `error-handling`).
10. New dependencies: check maintenance, provenance, license, known vulns, privilege footprint, and whether an existing dep suffices.
11. Automated scanners are incomplete evidence — still verify critical negative paths.
12. High-risk changes get a short threat/abuse pass (spoof, bypass, replay, IDOR, tenant leak).

## Workflow

1. Name assets, actors, trust boundaries, and sensitive data in the change.
2. Check authn/authz, tenant/ownership, input/output encoding, secrets, privacy, supply chain.
3. For high risk: list top abuse cases and the control that blocks each.
4. Add or update negative tests (unauthorized, wrong tenant, invalid input, replay) where feasible.
5. Record residual accepted risk with owner and revisit condition when shipping anyway.

## Anti-patterns

- UI permissions treated as authorization
- tenant isolation only on happy path
- `if (role === 'admin')` without resource checks
- secrets in examples/fixtures/logs
- adopting a dependency with no provenance/vuln glance
- returning stack traces or ORM errors to clients (`error-handling` / `api-contracts`)

## Done criteria

- [ ] Access and data paths are explicit for the touched operations
- [ ] Critical negative cases tested or explicitly justified
- [ ] No secrets/sensitive leaks in code, logs, or responses
- [ ] Accepted residual risk has owner (and expiry/revisit if material)

## Framework adapters

Apply only what the repo uses.

### NestJS

- Guards/policies for authz; never rely on frontend alone
- Prefer decorator + policy per resource; validate DTOs with `ValidationPipe`
- Secrets via `ConfigModule` / env — not hardcoded module constants

### Express

- Authn middleware + per-route authz on resource id
- Validate bodies/params/query with the project schema lib before use cases
- File uploads: size/type allowlist, storage outside exec path, no user-controlled paths

### Next.js

- Server Components/Actions/Route Handlers: authz on the server for every mutation
- Do not trust client-passed `userId`/`tenantId`; derive from session
- `NEXT_PUBLIC_*` is public — never put secrets there

### Prisma / Drizzle / Sequelize / TypeORM

- Scoped queries include tenant/owner constraints in the same operation that mutates/reads
- No raw SQL with string interpolation; parameterized only
- Migrations and seed data must not embed real secrets/PII

### React / SPA

- Hide/disable is UX only; assume API enforces
- Do not store long-lived secrets in `localStorage` unless the project already standardized a threat model for it
