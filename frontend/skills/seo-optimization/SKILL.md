---
name: seo-optimization
description: >-
  Apply metadata, JSON-LD, Open Graph, sitemap/robots, and shared SEO helpers
  for public routes. Use when adding public pages or fixing SEO. Skip private
  dashboard-only UI. Template — default examples use Next Metadata API.
---

# SEO Optimization

## Template

Copy into the target repo, then resolve every `ADAPT:`. Examples use **Next.js Metadata API**; map helpers to the project's meta/head mechanism if different.

## Purpose

Ship correct metadata and structured data for public routes without bloating route files.

## When to use

- public marketing/detail routes
- OG images, sitemap, robots
- sharing/social previews

## Do not use

- authenticated-only screens with `noindex` by policy (unless fixing leaks)
- pure visual polish with no crawl/share impact

## Rules

1. User-facing public pages define metadata (framework API or equivalent).
2. Builders live in `shared/seo/` (`ADAPT`) — pages call helpers.
3. Titles/descriptions follow **app locale** (`error-handling` locale rules).
4. Canonical URLs from shared route constants + site origin config — no hardcoding prod host in features.
5. OG/Twitter images: static or framework OG route beside the page.
6. JSON-LD matches visible content; no fake ratings/reviews.
7. `sitemap` / `robots` stay thin; data from features/services if needed.

## Page pattern (Next example)

```jsx
import { DomainView, getDomainData } from "@/features/<domain>";
import { generateDomainMetadata } from "@/shared/seo/metadata";

export async function generateMetadata({ params }) {
  const data = await getDomainData(params.id);
  return generateDomainMetadata(data);
}
```

## Checklist

- [ ] Unique title/description per indexable URL
- [ ] OG tags present for shareable pages
- [ ] JSON-LD valid when used
- [ ] Private routes not accidentally indexed
- [ ] No secrets in metadata
