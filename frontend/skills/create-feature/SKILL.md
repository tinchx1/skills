---
name: create-feature
description: >-
  Scaffold a new Screaming Architecture feature module under features/<domain>
  with the right optional folders, public barrel, and no cross-feature imports.
  Use when adding a domain. Skip tiny UI tweaks inside an existing feature.
  Template — adapt paths and optional folders.
---

# Create Feature

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults below are starter opinions, not project law.

## Purpose

Create a self-contained `features/<domain>/` module with a public `index` barrel.

## When to use

- new business domain (auth, billing, search, …)
- extracting a domain out of `shared/` or a bloated feature

## Do not use

- adding one component to an existing feature → `create-component`
- adding a route only → `create-page` after the feature exists

## Prerequisites

Read `screaming-architecture` (or `panel-spa-architecture`). Confirm domain name (`kebab-case`).

## Workflow

1. **Name the domain** — noun that “screams” the product.
2. **Create folder** `ADAPT: SRC/features/<domain>/`.
3. **Add only needed subfolders:**

| Folder | When |
|--------|------|
| `components/` | domain UI |
| `hooks/` | domain hooks |
| `services/` | API calls for RSC/BFF (default) |
| `api/` + `keys.ts` | only if project uses `react-query` |
| `actions/` | Server Actions (if framework supports) |
| `store/` | client state local to domain |
| `validations/` | schemas |
| `constants/` | domain enums/config |
| `utils/` | pure helpers |
| `pages/` | panel SPA screens (`panel-spa-architecture`) |

4. **Public API** — barrel exports only what routes and other layers may use.
5. **Wire route** — thin page/route imports from `@/features/<domain>`.
6. **Do not** import other features; elevate shared bits to `shared/`.

## Barrel example

```js
// features/<domain>/index.js
export { DomainView } from "./components";
export { useDomainData } from "./hooks/useDomainData";
export { getDomainById } from "./services/domain.service";
```

If using React Query, export **hooks** as the primary consumer API (see `react-query`); keep `api/` private.

## Checklist

- [ ] No cross-feature imports
- [ ] Barrel does not re-export unrelated internals
- [ ] Validations live with the domain (reuse `shared/validations` for common fields)
- [ ] Errors/API shapes follow project `error-handling` / `api-contracts`
