---
name: create-page
description: >-
  Create a thin App Router page under app/ that only composes features/ and
  shared/ (metadata, fetch, render). Use when adding routes. Skip
  feature-internal screens that are not routes. Template — adapt for Next
  version or non-Next routers.
---

# Create Page

## Template

Copy into the target repo, then resolve every `ADAPT:`. Default assumes **Next.js App Router**. For panel SPAs with `features/*/pages/`, compose in the routes layer instead — see `panel-spa-architecture`.

## Purpose

Add a route file that stays a **thin** composition layer.

## When to use

- new URL / route group page
- OG/metadata route companions when the framework supports them

## Do not use

- building domain UI → `create-component` / `create-feature`
- API route handlers → `api-contracts` + `http-boundary` / BFF patterns

## Rules

1. Route `page` file ≤ ~20 lines when possible.
2. Import **only** from feature barrels and `shared/`.
3. No cross-feature imports.
4. Prefer Server Component pages when using RSC; push interactivity into feature client components.
5. Export metadata helpers for user-facing public routes — see `seo-optimization`.
6. Authed routes: enforce guards in layout / middleware, not only in UI.

## Page sketch

```jsx
import { DomainView, getDomainData } from "@/features/<domain>";
// ADAPT: metadata helpers if public SEO matters

export default async function DomainPage({ params }) {
  const data = await getDomainData(params.id); // ADAPT: sync/async params
  return <DomainView data={data} />;
}
```

`ADAPT:` adjust if the page is client-driven (`react-query` + thin shell).

## Placement (ADAPT route groups)

| Kind | Path example |
|------|----------------|
| Public | `app/(public)/…/page.jsx` |
| Authed | `app/(dashboard)/…/page.jsx` |
| Admin | `app/(admin)/…/page.jsx` |

## Checklist

- [ ] Thin page — logic in feature
- [ ] Barrel imports only
- [ ] Metadata/SEO considered for public routes
- [ ] Auth boundary exists for private routes
