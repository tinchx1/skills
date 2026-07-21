# Backend templates

Skills and rules for NestJS / Express APIs. Keep thin — defer cross-cutting to personal skills.

Every skill has a **Template** banner: copy → resolve `ADAPT:` → pick Nest or Express.

## Skills

| Folder | Description |
|--------|-------------|
| `backend-module-structure` | Capability/module layout (Nest or Express) |
| `create-use-case` | One material operation per file |
| `http-boundary` | Controllers/routes stay thin; map errors once |

## Rules

Templates ship with `alwaysApply: false` — enable in the target API repo.

| File | Description |
|------|-------------|
| `backend-conventions.mdc` | Structure reminders |
| `http-handlers.mdc` | Thin handlers / boundary validation |

## Complement (from `global/skills/` — do not duplicate bodies)

Copy into the project; reference by name:

- `error-handling` — domain errors, ORM mapping, Problem Details
- `api-contracts` — OpenAPI, pagination, idempotency
- `security-engineering` — authz on resource, secrets
- `verification-loop` — tests/typecheck evidence
- `eng-intake` / `documentation-update` / `agents-md-authoring` / `technical-research` as needed
