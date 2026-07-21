# cursor-skills

Public, reusable **Cursor Agent Skills** and **Rules** templates for frontend and backend — plus **global** cross-cutting skills so teams do not depend on someone’s personal `~/.cursor/skills`.

Copy what you need into a project (`.cursor/skills/`, `.cursor/rules/`), then adapt stack-specific bits.

## Layout

```text
global/
  skills/     # Cross-cutting (errors, contracts, security, intake, …)
frontend/
  skills/     # FE agent skills
  rules/      # FE .mdc rule templates
backend/
  skills/
  rules/
```

## How to use in a new repo

1. Copy needed **global** skills → `.cursor/skills/<name>/SKILL.md` (start with `error-handling`, `api-contracts`, `security-engineering`, `verification-loop`)
2. Copy FE and/or BE pack skills + rules
3. Search for `ADAPT:` and resolve placeholders (paths, locale, stack, DS barrel)
4. Set `alwaysApply` / `globs` per rule — public templates default to **opt-in** (`alwaysApply: false`)
5. Point `AGENTS.md` at local skills by name — do not paste skill bodies into AGENTS
6. Trim folders/sections the project does not use

Every skill starts with a **Template** section: defaults are starter opinions, not project law.

## Global pack (recommended baseline)

| Skill | Role |
|-------|------|
| `eng-intake` | Scope / risk before non-trivial work |
| `error-handling` | Errors end-to-end |
| `api-contracts` | Public HTTP/events contracts |
| `security-engineering` | Authn/authz, secrets, trust |
| `verification-loop` | Checks + residual risk |
| `documentation-update` | Docs after material changes |
| `agents-md-authoring` | Repo `AGENTS.md` |
| `technical-research` | Version-sensitive research |

See [`global/README.md`](global/README.md).

## Frontend packs (pick one architecture)

### Next App Router + Screaming

| Skill | Role |
|-------|------|
| `screaming-architecture` | Dependency rules, folder map, barrels |
| `create-feature` | Scaffold a domain module |
| `create-page` | Thin `app/` route |
| `create-component` | Feature vs shared placement |
| `data-fetching` | Server fetch / actions / BFF |
| `seo-optimization` | Metadata, JSON-LD, OG |
| `react-query` | Optional client server-state |
| `loading-ux` | Shared loader + in-control pending |
| `clean-modular-responsibilities` | No inner components/hooks in pages |

Rules: `project-conventions`, `react-components`, `next-pages`, `styling-scss` **or** `styling-tailwind`, `api-patterns`, optional `react-query` / loading / clean-modular.

### Panel SPA (Vite + React Router style)

| Skill | Role |
|-------|------|
| `panel-spa-architecture` | Features, roles, thin routes |
| `create-form` | Zod + RHF + zodResolver |
| `react-query` | Client server-state |
| `atomic-shared-ui` | Prefer shared DS primitives |
| `loading-ux` / `clean-modular-responsibilities` | UX + structure |

Rules: `panel-spa`, `forms-rhf-zod`, `theming-tokens` (+ Query / atomic / loading as needed). Do not enable Next Screaming + panel SPA `alwaysApply` together blindly.

## Backend starter pack

Thin structure templates (NestJS or Express). Defer errors/contracts/security to **global** skills — copy those into the same project.

## License

MIT (or set your preferred license before publishing).
