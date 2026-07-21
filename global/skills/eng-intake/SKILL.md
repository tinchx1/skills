---
name: eng-intake
description: >-
  Use first for non-trivial engineering work to classify outcome, risk, delivery
  stage, constraints, assumptions, affected surfaces, evidence, and rollback
  before implementing. Skip tiny reversible edits and direct factual questions.
---

# Eng Intake

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Define scope, assess risk, decide evidence, and surface blockers before writing code.

## When to use

- bug, feature, refactor, integration, migration, or review
- scope or risk is unclear
- may touch API, data, auth, deploy, or multi-package boundaries

## Do not use

- trivial, local, reversible edits
- direct factual questions

## Procedure

1. Restate outcome, in-scope, and out-of-scope.
2. Capture constraints that are explicitly stated or directly implied by the existing codebase.
   Do not infer new product requirements.
3. List assumptions. If too many or high-impact: stop and ask.
4. Classify risk: `trivial | low | medium | high` + trigger.
   Raise for public API, persistent data, auth, payments, hard-to-reverse migrations, multi-app impact, prod deploy.
   If multiple risk triggers apply, use the highest risk.
5. Declare stage: `functional-build | integration | production-hardening | production-change`.
6. Classify work as `NOW | LATER | NOT PLANNED`.
7. List affected surfaces: code, contracts, data, auth/security, ops, docs, tests.
8. Define evidence and rollback before implementing.
9. Do not modify code until the task has been scoped.
10. Ask only blocking questions that materially affect correctness, safety, or architecture.
    Otherwise inspect the codebase and proceed.

## Expected output

```text
Outcome:
In scope:
Out of scope:

Constraints: (explicit only; omit N/A)
- ...

Assumptions:
- ...
(If assumptions are many or risky: stop and ask.)

Risk: low|medium|high — <trigger>
Stage: functional-build|integration|production-hardening|production-change

NOW:
LATER:
NOT PLANNED:

Surfaces:

Evidence:
- build passes
- typecheck passes
- affected tests pass
- lint passes if applicable
- format check passes if applicable
- no new warnings unless justified
- <extra proof required by risk/stage>

Rollback:
- revert strategy
- migration rollback (if applicable)
- feature flag or fallback (if applicable)

Open questions: (blocking only)
```

## Defaults

- Prefer `functional-build` unless the ask is explicitly integration/prod.
- Keep correctness floor in NOW: authz, data integrity, contract validation, focused tests.
- Defer advanced hardening unless risk/stage requires it.
- Do not expand scope beyond NOW.

## Anti-patterns

- modifying code before the task is scoped
- inventing constraints, stack, or assumptions
- treating all prod-hardening as a blocker for a functional slice
- asking non-blocking questions instead of inspecting the repo
- scope creep via unstated assumptions

## Done when

- outcome, risk, stage, and NOW/LATER are explicit
- constraints and assumptions are visible
- evidence and rollback are defined
- only blocking questions remain (or none)
- code has not been modified until scope is settled
