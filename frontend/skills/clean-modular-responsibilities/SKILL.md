---
name: clean-modular-responsibilities
description: >-
  Keep pages/screens thin and split UI into feature components/hooks/utils —
  no inner React components or hooks inside page files. Use when refactoring
  fat pages or reviewing structure. Skip one-line copy tweaks. Template —
  adapt path aliases.
---

# Clean Modular Responsibilities

## Template

Copy into the target repo, then resolve every `ADAPT:`. Path names (`pages/` vs `app/` + views) follow the architecture pack in use.

## Purpose

Enforce clean modularity by responsibility: routes/pages orchestrate; components/hooks/utils live in the feature.

## When to use

- page/screen files growing large
- reviewing PRs with inline components/hooks
- extracting sections from a screen

## Do not use

- tiny presentational edits with no structure smell

## Anti-patterns (HARD)

1. **Do not** define React components as inner functions inside route/page files or other components.
   - OK: tiny render helpers returning primitives (no hooks, no JSX tree).
   - NOT OK: `function Sidebar(){...}` / `const Dialog = () => ...` inside a page/component file.
2. **Do not** define custom hooks inside feature pages/component files → `features/<domain>/hooks/`.
3. **Do not** define non-trivial utilities inside pages/components → `features/<domain>/utils/`.

## When to split (pragmatic)

- A page/screen exports **one** top-level component and mostly orchestrates: data hooks + state + composing extracted sections.
- If a file exceeds **~200–250 LOC**, or has **2+** non-trivial UI sections → extract to `features/<domain>/components/`.
- DOM/event wiring → feature hooks.

`ADAPT:` on Next Screaming apps, apply the same to fat `*View` / screen components — not only route files. On panel SPAs, apply to `features/*/pages/`.

## Complements

- `screaming-architecture` / `create-component` / `create-page`
- `panel-spa-architecture` when the app uses `features/*/pages/`

## Done criteria

- [ ] No inner components/hooks in page/screen files
- [ ] Fat sections extracted under feature `components/`
- [ ] Hooks/utils in dedicated folders
