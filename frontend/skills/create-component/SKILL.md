---
name: create-component
description: >-
  Create a React component in the correct Screaming Architecture location
  (feature domain vs shared) with folder conventions. Use when adding UI.
  Skip backend or pure data-layer work. Template — adapt styling and forms.
---

# Create Component

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults below are starter opinions, not project law.

## Purpose

Place and scaffold UI in the right layer with consistent folder shape.

## When to use

- new UI piece (feature or shared)
- extracting a reusable piece from a feature into `shared/`

## Do not use

- new domain module → `create-feature`
- new route → `create-page`

## Placement decision

```text
Used by one domain only?     → features/<domain>/components/
Used by 2+ features / shell? → shared/components/ (ui/ or layout/)
```

**Never** put domain business UI in `shared/` prematurely. Elevate only when a second consumer appears (or is certain).

## Folder shape (ADAPT: pick one styling system)

**SCSS Modules** (rule: `styling-scss`):

```text
ComponentName/
├── ComponentName.jsx          # ADAPT: .tsx
├── ComponentName.module.scss
└── index.js                   # ADAPT: .ts
```

**Tailwind** (rule: `styling-tailwind`):

```text
ComponentName/
├── ComponentName.jsx          # utilities / cn / cva in file — no sibling stylesheet
└── index.js
```

Export from parent `components/index` and from feature barrel **only if** external consumers need it.
Do not mix both styling systems in one component.

## Server vs client (ADAPT: RSC apps)

- Default: Server Component (no directive) when using RSC
- Add `"use client"` only for state, effects, browser APIs, form libs, Query hooks, etc.
- Keep client leaves small; pass data from server parents when applicable
- CSR-only apps: ignore RSC rules; still keep leaves focused

## Forms (ADAPT)

Complex / validated forms → skill **`create-form`** (Zod + RHF + `zodResolver`).

| Kind | Suggested approach |
|------|--------------------|
| Multi-step / complex | `create-form` — schema in `schemas/` |
| Simple uncontrolled | Native input; no form lib |

User-visible validation messages follow app locale (`error-handling`).

## Checklist

- [ ] Correct layer (feature vs shared)
- [ ] No cross-feature imports
- [ ] Client boundary justified (if RSC)
- [ ] Styles follow project styling rule
- [ ] Accessible names/labels for interactive controls
