---
name: agents-md-authoring
description: >-
  Create or rewrite repo-local AGENTS.md with product scope, architecture
  snapshot, commands, golden paths, high-risk surfaces, and agent workflow
  rules. Use for new repos, legacy repos without agent guidance, monorepos, or
  stack-specific onboarding. Skip product implementation, inventing docs trees,
  or work that belongs in .cursor/rules or documentation-update patches.
---

# AGENTS.md Authoring

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Author a brief, repo-specific `AGENTS.md` that any compatible agent (Cursor, etc.) can follow: product scope, map, commands, boundaries, risks, golden paths, and what not to do — grounded in real repo files.

## Split with related skills

| Need | Use |
|------|-----|
| Create/rewrite `AGENTS.md` as the repo agent guide | **this skill** |
| Patch existing docs/`AGENTS.md` after a material change | `documentation-update` |
| Cursor-only always-apply / glob rules (`.cursor/rules/*.mdc`) | built-in `create-rule` |
| Personal skills under `~/.cursor/skills/` | `create-skill` (not this) |

`AGENTS.md` = versioned, tool-agnostic repo guidance for collaborators.  
`.cursor/rules/` = Cursor-specific enforcement. Do not duplicate the same prose in both; link or keep rules thinner.

## When to use

- new repo or greenfield bootstrap
- legacy repo with no (or useless) local agent instructions
- monorepo / multi-package workspace needing a map and per-package commands
- project with stack-specific golden paths or high-risk surfaces worth spelling out

## Do not use

- the question is already answered by existing accurate `AGENTS.md`
- the task is product implementation, not repo guidance
- content belongs in a standard, skill, ADR, OpenAPI, or runbook — link it, don't paste it
- facts cannot be confirmed from repo files (ask or mark assumptions)
- user only wanted a Cursor rule → `create-rule`

## Operating rules

1. **Tool-agnostic and self-contained** — no personal vault paths, private workflow names, machine-only paths, or unversioned global standards.
2. **Brief and operational** — commands, boundaries, risks, do-nots. Not a handbook dump.
3. **Source-backed** — prefer README, manifests, scripts, existing docs, contracts, CI. Separate facts from assumptions.
4. **Real commands only** — from `package.json`, `Makefile`, `docker-compose`, CI, README. Never invent scripts.
5. **Route to authorities that exist** — list only docs/folders that are present. Do not prescribe placeholder files (`docs/product.md`, etc.) the repo does not have.
6. **Link, don't duplicate** — if `LLM_CONTEXT.md`, architecture docs, or contracts exist, point to them. Optionally keep `LLM_CONTEXT.md` as a reading router for agent-heavy repos; don't fork a second README.
7. **Generated context is evidence, not authority** (MCP dumps, graphs, context packs).
8. **High-risk surfaces + golden paths + closure** — always include what not to do and how to close a material task.
9. **No secrets** — no tokens, credentials, private URLs, or real PII. Env var *names* only.
10. Tool-specific adapters (`CLAUDE.md`, Cursor rules) may point at `AGENTS.md`; they must not redefine conflicting repo rules.

## Discovery checklist

Before writing, inspect what exists:

- README / CONTRIBUTING
- package manifests (`package.json`, workspaces, lockfiles)
- `LLM_CONTEXT.md`, existing `AGENTS.md`, `CLAUDE.md`
- `docs/` (architecture, ADR, operations) — only real paths
- contracts / OpenAPI
- scripts, Makefile, turbo/nx pipelines
- tests and CI workflows
- deploy / Docker / infra entrypoints
- `.env.example` (names only)
- repo structure and package boundaries
- existing conventions (lint, format, commit)

## Workflow

1. Discover (checklist). Note monorepo packages and default working directories.
2. Draft from the template below — delete sections that don't apply; fill only with confirmed facts.
3. Mark assumptions explicitly; ask when a wrong guess would be costly (prod deploy, migrations).
4. Write/update root `AGENTS.md` (and package-level `AGENTS.md` only if the monorepo already uses that pattern or packages diverge hard).
5. If `CLAUDE.md` / Cursor rules exist, ensure they delegate rather than fork conflicting rules.
6. Report: file path, facts used, assumptions, open questions, drift (docs vs code).

## AGENTS.md skeleton

```md
# AGENTS.md - <Project>

Repository-specific product, architecture, commands, boundaries, risks, and
workflow rules for collaborators and compatible agents.

## Product scope

<What the product is and is not.>

## Repository map

| Path | Responsibility | Stack |
| --- | --- | --- |
| <path> | <responsibility> | <stack> |

## Architecture snapshot

<Current architecture as it exists in the repo — not aspirational.>

## Canonical sources

Start with whatever exists, e.g.:

- README.md
- LLM_CONTEXT.md (if present)
- docs/… (only real paths)
- contracts/ or OpenAPI (if present)

When docs and code disagree, inspect runtime/code and report drift.

## Golden paths

### Backend
<local structure and rules — Nest/Express/etc. as used here>

### Frontend
<Next/SPA structure and rules as used here>

### Data
<schema/migration rules; Prisma/Drizzle/TypeORM/Sequelize as used here>

### Operations
<deploy/runbook entrypoints that exist>

## High-risk surfaces

- auth/session/permissions
- persistent data/migrations
- public APIs/contracts
- PII/secrets/privacy
- deployment/runtime
- cross-package exports
- jobs/retries/idempotency
- performance hot paths

(Trim to what this repo actually has.)

## Commands

Only commands declared by repo files:

- install:
- dev:
- test:
- lint:
- build:
- migrate:
- e2e:

Do not run dependency installs, migrations, destructive scripts, long-lived
servers, or real-env commands without explicit approval.

## Local workflow

- trivial/low risk:
- medium/high risk:
- docs/ADR required when:
- review required when:

## Do not

- Do not make Big Bang refactors.
- Do not move files by aesthetics.
- Do not introduce empty layers.
- Do not bypass API/data contracts.
- Do not log secrets or PII.
- Do not invent commands or docs paths not in the repo.

## Closure

Every material task must report:

- changed files
- evidence run
- checks not run
- contract/data/security/operation impact
- residual risk
```

## Expected output

- proposed or updated `AGENTS.md` (path)
- assumptions vs repo facts used
- questions / unresolved doc-code drift
- whether `documentation-update` or `create-rule` follow-ups are needed

## Anti-patterns

- dumping global/personal standards into the repo file
- inventing commands or doc paths
- hiding assumptions as facts
- secrets, prod URLs with credentials, or real PII
- long tutorials that belong in skills or ADRs
- duplicating the same rules in `AGENTS.md` and `.cursor/rules/` verbatim
- rewriting `AGENTS.md` for every feature ship (that's `documentation-update` only when workflow/constraints changed)

## Done criteria

- [ ] Brief, repo-specific, source-backed
- [ ] Commands verified against manifests/scripts
- [ ] Only existing doc authorities linked
- [ ] High-risk surfaces and do-nots present
- [ ] No secrets
- [ ] Assumptions and drift called out
