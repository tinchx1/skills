---
name: atomic-shared-ui
description: >-
  Prefer shared UI primitives (button, field, empty, loading, shell) over
  one-off markup and duplicate feature styles. Use when building feature UI or
  extracting design-system pieces. Skip backend-only work. Template — map roles
  to the project barrel.
---

# Atomic / Shared UI

## Template

Copy into the target repo, then resolve every `ADAPT:`. The component inventory is **per design system** — do not treat example names as required.

## Purpose

Reusable controls and layout primitives live under `shared/components` (or the project DS package). Features compose them; they do not fork button/select/empty/loading patterns.

## When to use

- new forms, tables, dialogs, page chrome
- replacing raw controls when a shared primitive already covers the pattern

## Do not use

- one-off domain visuals with no shared equivalent yet (build in feature first; elevate when reused)

## Import (ADAPT barrel)

```jsx
import {
  /* ADAPT: only what the project exports */
} from "@/shared/components";
```

Prefer the barrel. Direct paths only for documented edge cases.

## Roles → shared primitive (ADAPT names)

Map need → existing shared component. Example names are illustrative:

| Need | Role | Example names (ADAPT) |
|------|------|------------------------|
| Primary / secondary actions | Button | `Button` |
| Filter / segment toggles | Button group | `ButtonGroup` |
| Labeled control wrapper | Form field | `FormField` |
| Text / search | Text input | `TextInput`, `Input` |
| Styled select | Select / menu | `Select`, `MenuSelect` |
| Empty lists | Empty state | `EmptyState` |
| Inline feedback | Alert | `Alert`, `Banner` |
| Confirm / destructive | Dialog | `AlertDialog`, `ConfirmDialog` |
| Page scaffold | Shell | `PageShell`, `AppShell` |
| Layout top bar | Header nav | layout-only; do not fork in features |
| Loading | Loader | see `loading-ux` |
| List pagination | Pagination | `Pagination`, `ListPaginationNav` |
| SR-only text | Visually hidden | `VisuallyHidden` |

If the role has no shared primitive yet: implement in the feature, elevate on second consumer.

## New shared component

1. `shared/components/<Name>/` + styles (`ADAPT:` SCSS modules / Tailwind / DS package)
2. Export from the shared barrel
3. Reuse design tokens — no one-off hex/spacing that bypasses the theme

## Forbidden

- raw `<button>` / `<select>` / duplicate chrome when a shared primitive covers it
- forking layout chrome (header, pagination shell) inside features

## Done criteria

- [ ] Shared primitive used when one exists
- [ ] New shared pieces exported from barrel
- [ ] Loading follows `loading-ux` (or project loader rule)
