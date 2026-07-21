---
name: documentation-update
description: >-
  Use after material changes to align durable docs with code and reality:
  inventory existing docs, update the authoritative artifacts, run a root-doc
  pass for drift, and verify links. Covers README, docs, changelog, runbooks,
  contracts, and AGENTS/LLM context when those files already exist. Skip trivia,
  comment-only edits, and inventing new documentation topology.
---

# Documentation Update

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Keep durable documentation aligned with the change: update the smallest authoritative artifacts and confirm the rest of the existing doc surface is not silently drifting.

## When to use

- behavior, API, data, architecture, operations, security, workflow, or release knowledge changed
- README, `docs/*`, changelog, runbook, contract docs, or existing agent-context files may be wrong
- closing a delivery/spec where documentation drift is likely
- docs and code diverge after a material change

## Do not use

- only internal comments or temporary notes changed
- implementation-only work with no durable knowledge change
- deciding or inventing repo documentation topology (new `docs/adr/`, `LLM_CONTEXT.md`, specs layout, sprawl cleanup)
- syncing knowledge into an external vault/Obsidian (out of scope)
- the user asked not to touch docs

## Procedure

1. Identify the material change from diff/status and confirm the authority (code, contract, tests, migration, ADR, runbook, or active spec).
2. Inventory existing documentation — do not assume filenames. Check root Markdown and relevant `docs/`, contracts, and runbooks that already exist.
3. Classify candidates as current, future, historical, operational, security, agent guidance, or generated evidence. Edit only artifacts whose owned knowledge changed.
4. Update the smallest authoritative artifact(s). Prefer patch over rewrite.
5. Run the **root-document pass** below for every root file that exists.
6. Search for stale statements/links: old names, removed paths, superseded status, contradicted capabilities.
7. Never put secrets, credentials, PII, exploit details, or sensitive URLs into docs.
8. Do not treat generated output (context packs, MCP dumps, build artifacts, graphs) as editable authority.
9. Verify with the cheapest repo-supported checks, then report.

## Root-document pass

Explicitly consider each file **only if it exists** (absence is not drift; do not create these mechanically):

- `README.md` — purpose, setup, commands, authority routing; keep concise
- `CHANGELOG.md` — user/operator-visible additions, fixes, security, breaking changes; git remains exact history
- `SECURITY.md` — reporting route and implemented baseline only when security knowledge changed
- `CONTRIBUTING.md` — setup, conventions, tests, delivery commands when those changed
- `AGENTS.md` — durable agent rules/commands/verification; update only when workflow or constraints changed, not merely because a feature shipped
- `CLAUDE.md` — adapter/router only; do not duplicate `AGENTS.md` when it already delegates
- `LLM_CONTEXT.md` — concise reading router / module map; do not turn it into a second README or architecture doc

## Authority rules

- Document authority, not volume.
- One fact → one primary home. Link elsewhere if needed.
- Changelog records what shipped; it is not architecture authority.
- If the repo already has ADRs or architecture docs, update those only when the change belongs there.
- Keep versioned docs tool-agnostic: no personal memory, private workflow names, or machine-specific paths.
- Repo-local templates win over any global habit.
- If no suitable durable doc exists: do not scaffold a docs tree; update README only when that is local convention, otherwise record residual risk.

## Verification

Prefer the cheapest checks the repo supports:

```text
git diff --check
search for conflict markers and stale terms
validate modified relative links / referenced paths
review git diff and status for scope
```

For contract, migration, security, or operational doc changes, also run applicable repo checks when they exist. Never claim docs are synced from a green test suite alone.

## Expected output

```text
Durable knowledge changed: yes|no

Authority selected:
- <path or "none">

Docs changed:
- <path> — <what updated>

Docs inspected, unchanged:
- <path> — <why>

Root pass:
- considered: <existing root files>
- created: none (or explicit exception with reason)

Sensitive-data check: pass|fail
Checks run / omitted:
- ...

Residual drift risk:
- ...
```

## Defaults

- Prefer existing README / `docs/*` / CHANGELOG over creating new files.
- Prefer "inspected, unchanged" over rewriting for completeness theater.
- Prefer "no doc change + residual risk" over empty scaffolding.
- Pair with `eng-intake` (docs in NOW?) and `verification-loop` (code evidence stays separate).

## Anti-patterns

- creating `docs/adr/`, specs trees, or `LLM_CONTEXT.md` just because another repo has them
- treating missing root files as drift that must be filled
- duplicating the same decision across README + architecture + changelog + agent context
- updating `AGENTS.md` / `LLM_CONTEXT.md` on every feature ship
- rewriting large docs for a small change
- editing generated files as source of truth
- mixing current state and future target state without labeling
- putting secrets in docs
- claiming sync from tests alone

## Done when

- durable docs match the change, or explicit no-update reasons are recorded
- existing root surfaces were considered
- authority is not silently duplicated
- no sensitive data was introduced
- residual drift risk is visible
