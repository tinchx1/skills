---
name: technical-research
description: >-
  Use for version-sensitive facts, SDK/API behavior, migrations, compatibility,
  security uncertainty, or material recommendations that go beyond local repo
  facts. Prefer primary sources and record versions/dates. Skip purely local
  facts and mechanical edits.
---

# Technical Research

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Produce reproducible, source-backed research that can safely inform an implementation or decision.

## When to use

- behavior depends on external version, SDK, protocol, vendor, or release notes
- conflicting evidence or tooling/compatibility uncertainty
- security, migration, or hard-to-reverse change needs external validation
- the response recommends, decides, or generalizes beyond a purely local repo fact
(unless the user explicitly opts out of external research)

## sDo not use

- local code/tests/config fully answer a factual question and no broader claim is made
- pure mechanical work with no external knowledge dependency
- choosing/replacing stack → use `technology-decision` (when available) after research
- closing a code change with checks → use `verification-loop`



## Procedure

1. State a concrete, falsifiable research question and the decision it affects.
2. Inspect local authorities first (code, lockfiles, configs, existing docs) for current repo facts.
3. Consult primary external sources for normative or version-specific claims unless the user opted out.
4. Record version, date, and commit/tag/edition/dialect when relevant.
5. Separate facts, inferences, hypotheses, gaps, and contradictions.
6. For security, protocols, data, or hard-to-reverse changes: corroborate with another primary source or a minimal reproducible experiment.
7. If evidence is unavailable, state the limitation and bound confidence — never imply research happened when it did not.
8. Never put secrets, PII, customer names, internal URLs, or proprietary code into external queries.



## Experiment rules

Only when source evidence is insufficient to settle the question:

- Discover scripts from the owning package's `package.json` (and workspace root if needed).
- Use the package manager already in the repo (`pnpm` / `npm` / `yarn`).
- Prefer existing scripts; never invent tooling that is not present.
- Run the cheapest check that can falsify the claim (focused test > typecheck > lint > build > e2e/migrate).
- Monorepo: filter to affected packages (`pnpm --filter`, `turbo run <task> --filter`); do not run the full workspace by default.
- This is not a full verification pass — hand off to `verification-loop` after implementation.

Common script names (use only if present): `test` / `test:ci` / `test:e2e`, `type:check` / `type-check`, `lint`, `build`, `start:dev` / `dev`, `migration:*` / `migrate`.

## Expected output

```text
Question: <falsifiable question>
Decision affected: <what this informs>

Sources (version / date):
- <primary source>
- ...

Confirmed facts:
- ...

Inferences / hypotheses:
- ...

Missing or conflicting evidence:
- ...

Experiment (if any):
- <command> → pass|fail|skipped (<why>)

Recommendation:
Confidence: high|medium|low
Review trigger: <what would invalidate this>
Limitation / opt-out: <if applicable>
```



## Anti-patterns

- treating community answers or model memory as authority
- recommending from memory alone when external research was not excluded
- omitting versions/dates on version-sensitive claims
- mixing facts and guesses without labels
- recommending irreversible action from weak evidence
- running a full verification suite under the guise of research
- leaking secrets or proprietary code into external queries



## Done when

- another engineer can reproduce the research
- uncertainty is resolved or explicitly bounded
- material recommendations cite primary evidence, an explicit opt-out, or a declared limitation with bounded confidence
- no sensitive data was exposed

