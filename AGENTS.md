# AGENTS.md - cursor-skills

This repo publishes **templates**, not a runnable app.

## Product scope

- **Is:** reusable Cursor skills/rules for FE/BE, adapted per project
- **Is not:** a framework, design system, or EOS/agency handbook dump

## Repository map

| Path | Responsibility |
|------|----------------|
| `frontend/skills/` | Frontend agent skills |
| `frontend/rules/` | Frontend `.mdc` rule templates |
| `backend/skills/` | Backend agent skills |
| `backend/rules/` | Backend `.mdc` rule templates |

## Golden paths

1. Prefer editing templates here; project copies diverge on purpose.
2. Keep skills under ~200–300 lines; progressive disclosure via short ADAPT blocks.
3. Reference global personal skills by name; never duplicate their full text.
4. Every template must state **when to use / when not to use**.
5. Every skill opens with a **Template** banner; defaults are starter opinions, not project law. Rules ship `alwaysApply: false` unless a project fork opts in.

## Do not

- Do not put secrets or private client names as if they were universal defaults.
- Do not invent stack requirements (e.g. force React Query on RSC-only apps).
- Do not commit `.env` or real credentials in examples.

## Closure

When changing a template: update README index if a skill is added/removed; keep FE/BE split clean.
