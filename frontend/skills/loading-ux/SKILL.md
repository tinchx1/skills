---
name: loading-ux
description: >-
  Standardize loading UI via one shared loader and in-button pending feedback
  for mutations/submits. Use when adding fetch pending states, Suspense
  fallbacks, or action buttons. Skip pure layout with no loading. Template —
  adapt path and loader API per project.
---

# Loading UX

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults below are starter opinions, not project law.

## Purpose

One shared loader for page/section loading; action feedback stays **inside** the triggering control when possible.

## When to use

- list/page/section pending states
- Suspense fallbacks
- submit/save/delete/mutation buttons

## Do not use

- static screens with no async work
- inventing a second spinner system beside the shared one

## Shared Loading (ADAPT path + API)

Canonical component: `ADAPT: shared/components/Loading` (or design-system equivalent).

```jsx
import { Loading } from "@/shared/components/Loading"; // ADAPT import

if (isLoading) return <Loading />;

<Suspense fallback={<Loading />}>...</Suspense>
```

**Prefer:** one shared loader for all pending UI.  
**Avoid:** ad-hoc spinners, library progress widgets, or raw “Loading…” blocks when the shared component exists.

## Action buttons (default pattern)

When an action triggers loading (mutation, submit, save, delete):

| Rule | Default | ADAPT |
|------|---------|--------|
| Placement | In-button / in-control | Global overlay only if product requires it |
| Content while pending | Spinner/loader only (no label swap) | Keep label + spinner if a11y/brand needs it |
| Size | Inline variant of shared loader | Match control height |
| Contrast | Light/dark prop for filled buttons | Map to theme tokens |
| Disable | `disabled={isLoading}` | Also block double-submit via mutation state |
| A11y | Stable `aria-label` / `aria-busy` | Follow app locale (`error-handling`) |

```jsx
<button type="button" disabled={isLoading} aria-busy={isLoading} aria-label="Save">
  {isLoading ? <Loading inline /> : <span>Save</span>} {/* ADAPT: contrast prop */}
</button>
```

## Done criteria

- [ ] Pending UI uses the shared loader (or documented exception)
- [ ] Action loading is in-control + disabled + accessible name
- [ ] ADAPT path/API resolved for the target repo
