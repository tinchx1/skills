---
name: verification-loop
description: >-
  Use to close code changes with evidence: discover and run the cheapest
  relevant checks (tests, typecheck, lint, format, build), report results,
  skipped checks, and residual risk. Use after implementing or when verifying
  a fix. Skip pure discussion with no artifact changes.
---

# Verification Loop

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Prove the change against its risk with the cheapest adequate evidence, then close with results and residual risk.

## When to use

- code, contracts, data, docs, or configuration changed
- a bugfix needs regression evidence
- closure must report checks and residual risk

## Do not use

- no artifact changed and the user only requested analysis
- evidence is fully owned by a stricter domain skill already in use

## Procedure

1. Read intake evidence/risk/surfaces if present; otherwise infer from the diff and stated outcome.
2. Identify the owning package(s) or app(s). In a monorepo, scope checks to affected packages — do not run the entire workspace by default.
3. Discover available scripts from the relevant `package.json` (and workspace root if needed). Prefer existing scripts; never invent tooling that is not present.
4. Select checks by risk, not coverage vanity. Map acceptance criteria to the cheapest focused checks.
5. Run the fastest local checks first, in this preference order when scripts exist:
   - affected tests (`test`, `test:ci`, or focused test command)
   - typecheck (`type:check`, `type-check`, or `typecheck`)
   - lint (`lint` or `lint:check`; avoid `lint:fix` unless asked)
   - format check (`format:check` or equivalent non-writing check; do not auto-format unless asked)
   - build (`build`)
6. Add focused extras only when risk requires them and scripts/tools exist (e.g. `test:e2e`, migration dry-run, contract/auth negative checks).
7. Fix failures or report blockers. Re-run failed checks after fixes.
8. Close with results, checks not run (+ why), residual risk, and follow-ups.
9. Do not claim done without this report.

## Script discovery rules

- Use the package manager already in the repo (`pnpm` / `npm` / `yarn`).
- Monorepo: prefer filtered commands (`pnpm --filter`, `turbo run <task> --filter`, `--affected`) over full-workspace runs.
- If a script is missing, skip it and record it under checks not run.
- If the repo has almost no verification scripts, use minimal manual evidence and state residual risk explicitly.
- Do not invent technologies or CI jobs that are not in the repo.

## Risk-based extras

- Bugfix: add or update a regression test when viable and stable.
- Public API / contracts: positive + negative/auth/compatibility checks when feasible.
- Data migrations: representative path, failure/restart note, rollback note.
- Auth/security surfaces: negative authorization checks when feasible.
- Do not expand into full prod hardening unless intake stage/risk requires it.

## Expected output

```text
Risk / surfaces: <from intake or inferred>

Checks run:
- <command> → pass|fail (<brief>)
- ...

Manual evidence (if any):
- ...

Checks not run:
- <check> — <why>

Residual risk:
- ...

Follow-ups:
- ...
```

## Defaults

- Cheapest adequate evidence over slow broad suites.
- Prefer `format:check` over rewriting files with `format`.
- Prefer `lint` / `lint:check` over `lint:fix` unless the user asked to fix.
- Report new warnings; do not treat a noisy pass as clean unless justified.
- Pair with `eng-intake`: verify what intake marked as Evidence / NOW.

## Anti-patterns

- running the whole monorepo when one package changed
- inventing scripts or tools not in the repo
- broad slow suites while skipping the risky behavior
- reporting coverage instead of behavior
- hiding flaky failures
- omitting checks not run
- auto-formatting or lint-fixing the tree unsolicited
- closing with "looks good" and no command results

## Done when

- evidence matches risk
- failures are fixed or explicitly blocked
- checks not run are visible with reasons
- residual risk is visible
