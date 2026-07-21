---
name: react-query
description: >-
  Organize TanStack Query by Screaming feature: keys factory, pure api
  functions, hooks as public API, cache updates, pagination, and mutations.
  Use when the project adopts client server-state. Skip RSC-only apps when
  services suffice. Template — adapt folder names and defaults.
---

# TanStack Query — Server State

## Template

Copy into the target repo, then resolve every `ADAPT:`. Layout below applies **when this skill is adopted** — not for every frontend.

## Purpose

TanStack Query is the **single source of truth for client-held server state**, organized **by feature/domain**. Complements `screaming-architecture` / `panel-spa-architecture` and `data-fetching`; does not replace server-rendered reads unless the project standardizes on client fetching.

## When to use

- interactive lists/filters, polling, optimistic UX, shared client observers
- project already depends on `@tanstack/react-query`

## Do not use

- RSC-first pages with no client cache need → `data-fetching` services
- global UI/auth chrome → client store in `shared/store`, not Query
- duplicating Query results into Redux/Zustand

## Feature structure (when this skill applies)

```text
src/features/<domain>/
  keys.ts                 # query key factory
  api/                    # ADAPT: some repos use services/
    get-<entity>.ts
    …
  hooks/
    use-<entity>.ts
    …
  components/             # optional
  index.ts                # export hooks (public API)
```

Same purity rules if the folder is named `services/` instead of `api/`.

## Core rules

- Components consume **hooks only** — never call `api/` directly
- Hooks are not defined inside components/pages
- One hook = one use case
- Features do not import other features' `api/` or `keys`

## Query keys

```ts
export const entityKeys = {
  all: ["entities"] as const,
  lists: () => [...entityKeys.all, "list"] as const,
  list: (filters: unknown) => [...entityKeys.lists(), filters ?? {}] as const,
  details: () => [...entityKeys.all, "detail"] as const,
  detail: (id: string) => [...entityKeys.details(), id] as const,
};
```

- Array-based, hierarchical
- Stable filter objects
- Never hardcode keys outside the feature
- Do **not** invalidate entire domain (`keys.all`) unless unavoidable

## API layer

- Pure functions only — no React Query, no cache touches
- One file per use case
- Encapsulate fetch/HTTP client

## Queries

```ts
export function useEntity(id: string, options?) {
  return useQuery({
    queryKey: entityKeys.detail(id),
    queryFn: () => getEntity(id),
    staleTime: 1000 * 60 * 5, // ADAPT defaults
    ...options,
  });
}
```

Default `staleTime` guidance: lists 10–60s · details 2–10m · static 30m+.

### Pagination

Include page/filters in `queryKey`. Prefer `useInfiniteQuery` for infinite scroll; `useQuery` for page numbers. Use framework-appropriate `placeholderData` / keep-previous helpers for the installed Query major version.

## Mutations

Prefer `setQueryData` for known entities; invalidate **narrow** keys (lists), never whole domain by default.

Optimistic updates: `onMutate` cancel + snapshot → `onError` rollback → `onSettled` narrow invalidate.

## Prefetch

Hover / route transitions via `queryClient.prefetchQuery` with feature keys + api fn.

## Naming

Use case files: `use-entity.ts`, `get-entity.ts` — not blob files that mix all queries/apis.

## Public API

```ts
export * from "./hooks/use-entity";
export * from "./hooks/use-entities";
export * from "./hooks/use-update-entity";
```

## Anti-patterns

- API calls in components
- Hooks declared in pages
- Hardcoded query keys
- Invalidating `keys.all` casually
- Mixing domains in one key factory
- Duplicating server state in Zustand

## Done criteria

- [ ] keys + api/services + hooks layout present
- [ ] components only use hooks
- [ ] cache updates are narrow
- [ ] errors surfaced per `error-handling`
