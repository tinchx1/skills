---
name: panel-spa-architecture
description: >-
  Vite/React panel SPA layout: features with pages, role shells, thin route
  composition, client server-state vs UI state split. Use for admin/panel SPAs.
  Skip Next App Router Screaming apps unless migrating consciously. Template —
  adapt paths and stack per project.
---

# Panel SPA Architecture

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults below are starter opinions for panel SPAs, not project law. Client names, frozen modules, and migration exclusions belong in the **target repo** rules — not here.

## Purpose

Architecture for **panel/dashboard SPAs** (Vite + React Router style): domain features, role layouts, thin route composition, clear server-state vs client-state split.

## When to use

- React panel with `features/`, role shells, and a thin routes layer
- Client-held server state (typically TanStack Query)

## Do not use

- Next.js App Router Screaming default → `screaming-architecture`
- forcing client Query onto an RSC-only app

## Structure (ADAPT names)

```text
features/<domain>/     # business screens — pages/, components/, hooks/, services/, store?
roles/<role>/          # layout + Outlet only — no domain pages
app/routes/            # composition only — lazy pages from features, layouts from roles
shared/ | lib/         # cross-cutting
assets/icons/          # optional icon pipeline
```

**Hard:** no domain logic, data fetching, or feature services in `app/` or `roles/`.

Path constants: single source for links and `<Route>` defs (`ADAPT:` path).

## Icons (ADAPT bundler)

Prefer an SVG-as-component pipeline (e.g. SVGR) over hand-rolled chrome SVGs. Use `currentColor` for themeable strokes/fills when applicable.

## Server state vs client state

| Kind | Default | Notes |
|------|---------|--------|
| Server entities/lists | TanStack Query (see `react-query`) | Single source of truth for client-held server data |
| UI chrome / wizards / session mirror | Zustand (or equivalent) | **Not** API responses |

- Components consume **hooks**, not raw `useQuery` / fetch, when the project uses the Query skill layout
- Mutations invalidate **narrow** keys
- Do not sync Query/fetch results into client stores “for convenience”

## Data layer

`services/` (API) → `hooks/` (Query) → components. Normalize before use. Mocks match real API shapes when present.

## Components

Presentational vs container; no embedded fetching in presentational leaves. Prefer `atomic-shared-ui` + `clean-modular-responsibilities`.

## UI display (ADAPT locale)

Pick project conventions once and stick to them, e.g.:

- Missing / empty values: consistent placeholder (`N/A`, `—`, localized string) — **one** choice
- User-facing dates: consistent format + timezone policy (`error-handling` / locale rules)

Do not invent per-feature formats.

## Naming (defaults)

| Kind | Pattern |
|------|---------|
| Hooks | `useSomething` |
| Query | `use<Entity>` |
| Mutation | `use<Action><Entity>` |
| Store | `use<Something>Store` |
| Service | `<entity>.service.ts` |

## Migration strategy

- Migrate feature by feature; keep diffs minimal if it works
- Replace state gradually: Query first, then client store if needed
- `ADAPT:` list frozen modules / out-of-scope areas **in the project rule**, not here

## Complements

`react-query`, `loading-ux`, `atomic-shared-ui`, `clean-modular-responsibilities`, `error-handling`

## Done criteria

- [ ] Domain code only under `features/`
- [ ] Roles/routes stay thin
- [ ] Server state vs client state split respected
- [ ] Display/locale conventions set once for the project
