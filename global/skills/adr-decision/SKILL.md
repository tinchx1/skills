---
name: adr-decision
description: >-
  Detect architecture-significant decisions and write a compact ADR when
  changing boundaries, providers, persistence, public contracts, security,
  deployment, or hard-to-reverse dependencies. Skip trivial local choices.
---

# ADR Decision

## Template

Cross-cutting skill — copy into `.cursor/skills/<name>/` (team/project) or `~/.cursor/skills/` (personal). Prefer one shared version per team; do not fork per feature.

## Purpose

Record architecture-significant decisions as compact, immutable ADRs — rationale and consequences, not meeting minutes.

## When to use

- changing architecture, module boundaries, deployment topology, cloud/provider, or persistence
- changing a public API/event contract in a breaking or hard-to-reverse way
- adopting/replacing a dependency or pattern that locks the team in
- accepting an exception to a project `MUST` / hard rule
- security, privacy, data retention, or operations materially affected

## Do not use

- local, reversible, obvious choices already covered by conventions
- research with no decision yet → `technical-research` first
- user asked only for implementation and no ADR trigger exists
- inventing `docs/adr/` in a repo that has no docs practice → ask owner; otherwise note residual risk (`documentation-update` does not scaffold trees)

## Complement

| Need | Skill |
|------|--------|
| Gather versioned facts before deciding | `technical-research` |
| Patch README/AGENTS after the decision ships | `documentation-update` |
| Risk/scope before large work | `eng-intake` |
| Contract/error/security detail of the change | `api-contracts` / `error-handling` / `security-engineering` |

## Operating rules

1. **Trigger ≠ file count** — significance is impact/reversibility, not LOC.
2. Always consider **do nothing / status quo** as an option when viable.
3. ADRs record **decision + context + consequences**, not a full discussion log.
4. **Accepted ADRs are immutable** — correct by superseding with a new ADR that links the old one.
5. Exceptions to MUST rules need: rule, scope, compensating controls, approver, expiry/revisit.
6. Prefer the **repo’s existing ADR template/location**. If none exists and the owner wants ADRs, use the template below under `docs/adr/` (or the path they choose) — do not invent a parallel docs taxonomy quietly.
7. Separate facts from assumptions; link evidence (spikes, RFCs, contract diffs).

## Workflow

1. Name the decision and why it is architecture-significant.
2. List drivers, constraints, and quality scenarios (perf, security, operability, cost).
3. Compare real options (including status quo) — no fabricated strawmen.
4. State decision, consequences (good/bad), validation plan, reversibility, exit/migration.
5. Write the ADR file; link related specs/contracts/follow-ups.
6. If implementing in the same change set: implement against the decision; close with `verification-loop` / `documentation-update` as needed.

## Template (ADAPT path/numbering)

```markdown
# ADR-<NNN>: <Title>

- Status: Proposed | Accepted | Superseded by ADR-<NNN> | Deprecated
- Date: YYYY-MM-DD
- Deciders: <names or roles>

## Context

<Problem, forces, constraints.>

## Decision drivers

- <driver>

## Options considered

### Option A — <name> (status quo if applicable)
- Pros: …
- Cons: …

### Option B — <name>
- Pros: …
- Cons: …

## Decision

<Chosen option and concise rationale.>

## Consequences

- Positive: …
- Negative / risks: …
- Neutral: …

## Validation

<How we will know it worked — metrics, tests, rollout checks.>

## Reversibility / exit

<How hard to undo; migration or escape hatch.>

## Links

- Specs/contracts/PRs/spikes: …
```

## Anti-patterns

- ADRs for trivia (rename helper, move two files)
- only listing benefits of the chosen option
- editing an Accepted ADR in place instead of superseding
- copying EOS/handbook essays into the ADR body
- ADR without status quo when it was a real alternative

## Done criteria

- [ ] Trigger accepted or explicitly ruled out (with reason)
- [ ] ADR has context, drivers, options, decision, consequences, validation, exit/review
- [ ] Status and links present; supersession used when replacing a prior ADR
- [ ] Implementation (if any) matches the recorded decision
