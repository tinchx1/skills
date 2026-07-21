---
name: screaming-architecture
description: >-
  Apply Screaming (feature/domain) architecture for App Router-style frontends:
  thin routing layer, self-contained features/, shared/ cross-cutting, and
  dependency direction. Use when scaffolding frontend structure, adding
  features, reviewing imports, or adapting this template to a project.
  Skip pure styling tweaks or backend-only work.
---

# Screaming Architecture (Frontend)

## Template

Copy into the target repo, then resolve every `ADAPT:`. Default pack assumes **Next.js App Router**; swap routing/layout for Vite SPA or other routers. Defaults below are starter opinions, not project law.

## Purpose

Keep the routing layer thin. Business UI, hooks, and domain logic live in `features/<domain>/`. Cross-cutting code lives in `shared/`.

## Complement

Does not replace: `error-handling`, `api-contracts`, `security-engineering`, `create-feature`, `create-page`, `data-fetching`, `react-query`. For Vite panels → `panel-spa-architecture`.

## When to use

- greenfield frontend or migrating to feature folders
- deciding where a file belongs (route vs feature vs shared)
- reviewing cross-feature imports
- writing/updating project `AGENTS.md` architecture snapshot

## Do not use

- backend-only changes
- one-line UI copy with no structure impact
- forcing this layout onto an incompatible legacy tree without an owner decision

## Dependency rules (HARD)

```text
app/        → features/ | shared/
features/   → shared/ only
features/   ✗ other features/
shared/     ✗ features/
```

`ADAPT:` rename `app/` if the router uses `pages/` or `routes/`.

If two features must communicate: elevate shared logic to `shared/`, or use an intentional global client store — never import feature internals across domains.

## Public API

Each feature exposes **only** `features/<domain>/index.{js,ts}` (barrel).

```js
// OK
import { PropertyDetail, getPropertyById } from "@/features/properties";

// FORBIDDEN
import { PropertyGallery } from "@/features/properties/components/PropertyGallery";
```

## Reference tree (ADAPT)

`ADAPT: PROJECT_ROOT` default `src/`. Route groups are examples — rename to the product.

```text
src/
├── app/                          # thin routing only (ADAPT: Next)
│   ├── layout.jsx
│   ├── (public)/…/page.jsx
│   ├── (dashboard)/…/page.jsx
│   └── api/                      # BFF handlers (optional)
├── features/
│   └── <domain>/
│       ├── components/
│       ├── hooks/
│       ├── services/             # OR api/ if using react-query
│       ├── actions/              # Server Actions (optional)
│       ├── store/                # client state local to domain
│       ├── validations/
│       ├── constants/
│       ├── utils/
│       └── index.js              # public API
├── shared/
│   ├── components/ui/
│   ├── components/layout/
│   ├── hooks/
│   ├── services/
│   ├── store/
│   ├── utils/
│   ├── constants/
│   ├── validations/
│   ├── seo/                      # if public SEO matters
│   └── styles/
└── middleware.js                 # ADAPT: if framework supports it
```

Create **only** subfolders a domain needs.

## Thin route / page

Max ~20 lines when possible: metadata + fetch/compose. No business UI definitions in the route file.

```jsx
import { DomainView, getDomainData } from "@/features/<domain>";

export default async function DomainPage({ params }) {
  const data = await getDomainData(params.id);
  return <DomainView data={data} />;
}
```

## Stack defaults (ADAPT per project)

| Concern | Default in this pack | Alternatives |
|---------|----------------------|--------------|
| Framework | Next.js App Router | Vite SPA → `panel-spa-architecture` or drop `app/` |
| Components | Server Components default; `"use client"` only for interactivity | CSR-only apps: ignore RSC rules |
| Styles | Pick **one**: SCSS Modules **or** Tailwind | Copy matching rule only |
| Client state | Small stores for UI/auth/wizard — **not** server cache | Redux only if legacy |
| Server state (client) | Prefer RSC + `services/`; `react-query` when interactive cache needed | Query-first panels OK |
| Validation | Shared schemas (e.g. Zod) server/client | Project validator of choice |
| Forms | Complex → form lib + schema; simple → server action / progressive enhancement | ADAPT libs |

## Anti-patterns

- business logic or large JSX in route `page` files
- cross-feature imports
- deep imports past feature barrel
- putting domain components in `shared/` “just in case”
- duplicating server data in client stores when Query/RSC owns it
- inventing empty layers (`services/` with no calls)

## Done criteria

- [ ] New code respects dependency direction
- [ ] Feature public API is the barrel only
- [ ] Route files stay thin
- [ ] ADAPT markers resolved for the target repo
- [ ] Related skills chosen: `data-fetching` and/or `react-query`
