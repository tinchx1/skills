---
name: create-form
description: >-
  Scaffold a React form with Zod schema, react-hook-form, and zodResolver.
  Use when adding or restructuring forms. Skip read-only UI or trivial
  uncontrolled inputs. Template — adapt paths and DS Form primitives.
---

# Create Form

## Template

Copy into the target repo, then resolve every `ADAPT:`. Defaults assume **Zod +
react-hook-form + `@hookform/resolvers/zod`**. Pair with rule `forms-rhf-zod`.

## Purpose

One consistent path from schema → typed values → validated form → submit.

## When to use

- new create/edit form, dialog form, or multi-field filter that needs validation
- migrating ad-hoc validation to Zod + RHF

## Do not use

- single uncontrolled search box with no schema
- server-only validation with no client form state
- pure display components → `create-component`

## Stack (fixed)

| Lib | Role |
|-----|------|
| `zod` | Schema + `z.infer` types |
| `react-hook-form` | Form state |
| `@hookform/resolvers/zod` | `zodResolver(schema)` |
| DS Form primitives | shadcn `Form` / `FormField` or project equivalent (`ADAPT`) |

Do not dual-validate (manual checks that duplicate the schema).

## Placement (ADAPT)

```text
domain/<x>/schemas/<name>.schema.ts   # or features/<x>/schemas/
domain/<x>/components/<Name>Form.tsx  # form UI
domain/<x>/hooks/use<Action>.ts       # mutation / submit (react-query)
```

Cross-domain shared schemas only when two domains truly share the contract.

## Workflow

1. **Schema** — define Zod object; export `type X = z.infer<typeof xSchema>`.
2. **Form** — `useForm({ resolver: zodResolver(schema), defaultValues })`.
3. **Fields** — wire via DS Form helpers (`FormField`, `Controller`, etc.).
4. **Submit** — `handleSubmit` → domain mutation hook; map API errors with `error-handling`.
5. **UX** — disable submit while pending; in-control loader (`loading-ux`).

## Example shape

```ts
// domain/auth/schemas/login.schema.ts
import { z } from 'zod'

export const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
})
export type LoginValues = z.infer<typeof loginSchema>
```

```tsx
// domain/auth/components/LoginForm.tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { loginSchema, type LoginValues } from '../schemas/login.schema'

const form = useForm<LoginValues>({
  resolver: zodResolver(loginSchema),
  defaultValues: { email: '', password: '' },
})
```

## Locale

User-visible Zod messages follow app locale (`error-handling`). Prefer schema
`.message` / refinements in the product language when the UI is localized.

## Done criteria

- [ ] Schema in `schemas/`; types from `z.infer`
- [ ] `zodResolver` is the only client validation bridge
- [ ] Form UI in domain/shared layer correctly
- [ ] Submit goes through domain hook / API, not raw fetch in the form
- [ ] Pending + error UX present
