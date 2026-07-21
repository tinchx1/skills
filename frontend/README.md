# Frontend templates

Skills and rules for Screaming-style frontends. **Next.js App Router** and **panel SPA** are alternate packs — pick one architecture per app.

Every skill has a **Template** banner: copy → resolve `ADAPT:` → defaults are opinions, not law.

## Skills

| Folder | Pack | Description |
|--------|------|-------------|
| `screaming-architecture` | Next | Thin routing, features, shared |
| `panel-spa-architecture` | Panel SPA | Features, roles, routes, state split |
| `create-feature` | Both | New domain module |
| `create-page` | Next | Thin `app/` page |
| `create-component` | Both | Feature vs shared component |
| `data-fetching` | Next (also useful elsewhere) | Server fetch, actions, BFF |
| `seo-optimization` | Next / public sites | Metadata / JSON-LD / OG |
| `react-query` | Optional / Panel | TanStack Query layout |
| `loading-ux` | Both | Shared loader + in-control pending |
| `clean-modular-responsibilities` | Both | No inner components/hooks in pages |
| `atomic-shared-ui` | Panel (useful anywhere) | Prefer shared DS primitives |

## Rules

Copy `.mdc` files into the project's `.cursor/rules/`. Templates ship with `alwaysApply: false` — enable what the repo needs.

| File | Notes |
|------|-------|
| `project-conventions.mdc` | Screaming dependency direction |
| `panel-spa.mdc` | Panel SPA (alt to Next screaming) |
| `react-components.mdc` | RSC / client policy |
| `next-pages.mdc` | Thin App Router pages |
| `styling-scss.mdc` / `styling-tailwind.mdc` | Pick **one** |
| `api-patterns.mdc` | Feature services/api |
| `react-query.mdc` | Query keys/hooks |
| `loading-component.mdc` | Shared Loading |
| `loading-action-button.mdc` | In-control loader |
| `clean-modular-responsibilities.mdc` | Anti-inner-components |
| `atomic-components.mdc` | Shared UI roles |

## Complement (from `global/skills/` — do not duplicate bodies)

Copy into the project; reference by name:

- `error-handling` — user-facing errors, locale, no leaks
- `api-contracts` — BFF/route handler contracts
- `security-engineering` — auth on server, no secrets in public env
- `verification-loop` — lint/test/build evidence
- `eng-intake` / `documentation-update` / `agents-md-authoring` / `technical-research` as needed
