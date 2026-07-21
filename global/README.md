# Global skills

Cross-cutting agent skills used by **both** frontend and backend packs. Copy these first — FE/BE templates reference them by name and do not duplicate their bodies.

## Skills

| Folder | Role |
|--------|------|
| `eng-intake` | Scope, risk, evidence, rollback before coding |
| `error-handling` | Domain errors, API mapping, UI error UX |
| `api-contracts` | HTTP/events contracts, compatibility, idempotency |
| `security-engineering` | Authn/authz, secrets, trust boundaries |
| `verification-loop` | Cheapest checks + residual risk |
| `documentation-update` | Keep durable docs aligned after material changes |
| `agents-md-authoring` | Repo-local `AGENTS.md` structure |
| `technical-research` | Version-sensitive facts, primary sources |
| `adr-decision` | When to write ADRs + compact template |

## How to install

```bash
# Into a project (shared with the team)
cp -R global/skills/<name> <repo>/.cursor/skills/<name>

# Or personal (all your machines/projects)
cp -R global/skills/<name> ~/.cursor/skills/<name>
```

Resolve any stack-specific notes inside each skill for the target repo. These are still **templates**: no client names, no secrets.
