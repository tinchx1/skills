# cursor-skills

Public, reusable **Cursor Agent Skills** and **Rules** templates for frontend and backend.

Copy what you need into a project (`.cursor/skills/`, `.cursor/rules/`), then adapt stack-specific bits. These templates **complement** personal/global skills such as `error-handling`, `api-contracts`, `security-engineering`, `verification-loop`, `agents-md-authoring` — they do not replace them.

## Layout

```text
frontend/
  skills/     # Agent skills (SKILL.md)
  rules/      # Cursor rule templates (.mdc)
backend/
  skills/
  rules/
```

## How to use in a new repo

1. Copy relevant skills → `.cursor/skills/<name>/SKILL.md`
2. Copy relevant rules → `.cursor/rules/<name>.mdc`
3. Search for `ADAPT:` and resolve placeholders (paths, locale, stack, DS barrel)
4. Set `alwaysApply` / `globs` per rule — public templates default to **opt-in** (`alwaysApply: false`)
5. Point `AGENTS.md` at local skills + your global ones — do not paste skill bodies into AGENTS
6. Trim folders/sections the project does not use

Every skill starts with a **Template** section: defaults are starter opinions, not project law.

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
| `react-query` | Client server-state |
| `atomic-shared-ui` | Prefer shared DS primitives |
| `loading-ux` / `clean-modular-responsibilities` | UX + structure |

Rules: `panel-spa` (+ Query / atomic / loading as needed). Do not enable Next Screaming + panel SPA `alwaysApply` together blindly.

## Backend starter pack

Thin structure templates that assume NestJS or Express and defer errors/contracts/security to global skills.

## License

MIT (or set your preferred license before publishing).
