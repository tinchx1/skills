---
name: data-fetching
description: >-
  Choose and implement frontend data loading for Screaming-style apps: server
  fetch via feature services, mutations/actions, BFF handlers, and when to hand
  off to TanStack Query. Use when adding or refactoring data flows. Skip pure
  presentational UI. Template — adapt to RSC vs CSR.
---

# Data Fetching

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults favor RSC + services; Query-first panels are valid — see `react-query` / `panel-spa-architecture`.

## Purpose

Pick the right loading path without mixing responsibilities or leaking server state into UI stores.

## When to use

- new read/write from UI or route page
- migrating ad-hoc fetch into services or Query
- BFF / proxy route handlers

## Do not use

- styling-only or static content
- full Query setup details → `react-query`

## Decision tree

```text
URL-driven, SEO, or server-rendered read?
  → route/layout calls features/<domain>/services/*
  → pass props into components

Interactive client cache (filters, polling, optimistic, shared client observers)?
  → react-query (api/ + hooks/ + keys)
  → components consume hooks only

Simple mutation?
  → server action / form action + schema  OR  Query mutation hook

External webhook / secret-bearing proxy?
  → server route handler (BFF); validate; never expose secrets to client
```

## Services layer (default for server reads)

- Pure functions: no React, no cache APIs
- Live in `features/<domain>/services/`
- Shared HTTP client in `shared/services/` (`ADAPT` path)
- Map errors at boundary per `error-handling` / `api-contracts`
- Export from feature barrel what pages need

```js
// features/<domain>/services/<entity>.service.js
export async function getEntityById(id) {
  const res = await api.get(`/entities/${id}`);
  return res.data;
}
```

## Mutations / server actions (ADAPT)

- Validate with domain schemas
- Return stable error codes / field errors — not raw exceptions
- Revalidate tags/paths intentionally when the framework supports it

## BFF route handlers

- Stay thin: parse → call service/use-case → map response
- AuthZ on server; align with `security-engineering`
- Same public error contract as the API

## Client state vs server state

| Data | Store |
|------|--------|
| Server entities/lists | Server props and/or React Query |
| Auth session mirror / UI chrome | Small client store (`shared/store`) |
| Wizard draft before submit | Feature store or form state — not a second server cache |

**Forbidden:** syncing Query / fetch results into a client store “for convenience”.

## Checklist

- [ ] Path chosen deliberately (server / action / Query / BFF)
- [ ] No API calls from presentational components when hooks/services exist
- [ ] Errors mapped; no ORM/HTTP internals to UI
- [ ] Secrets stay server-side
