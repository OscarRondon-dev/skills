# Resume Project — Examples

## Example invocation

> `/resume-project` — retomar Komtexia, lleva ~2 meses parado, quiero levantar dev hoy

## Example Snapshot (after recon)

| Area | Finding |
| --- | --- |
| Last activity | 67 days ago — `feat(auth): add login flow` |
| Git state | On `feature/auth`, 3 uncommitted files, no stash |
| Stack | Angular 19, NestJS 10, Prisma |
| Standards present | yes — `.cursor/rules/` (6 rules), no ADRs |
| Obvious risks | `@angular/core` major behind; `.env` not in repo (good) |

## Example grilling round 1

```
❓ **Q1** - **Session goal**: Do you need a running dev environment today, or a planning/architecture refresh first?

➡️ Running dev environment — validate auth branch still builds.

❓ **Q2** - **Branch authority**: Is `feature/auth` still the line of work to continue, or should we rebase onto `main` first?

➡️ Continue on `feature/auth` unless main has critical fixes.

❓ **Q3** - **Upgrade appetite**: OK to bump patch/minor deps if install fails, or strictly no dependency changes today?

➡️ Patch/minor only if required to run; no major upgrades this session.
```

## Example brief excerpt

```markdown
## First 60–90 minutes (ordered)

1. [ ] `git status` — commit or stash the 3 WIP files (user decides)
2. [ ] `npm ci` in api and web — note failures
3. [ ] Copy `.env.example` → `.env` if missing; confirm DB reachable
4. [ ] `npm run start:dev` — capture first error only, fix minimally

## Do not touch yet

- Major Angular upgrade
- Auth architecture redesign
- New `.cursor/rules`
```

## When to delegate

| Brief finding | Next skill |
| --- | --- |
| No `.cursor/rules`, no CODING_STANDARDS | `setup-repo-standards` |
| Prisma patterns inconsistent with v6 docs | `extend-domain-standards` + "Prisma" |
| Domain boundaries unclear after pause | `grill-with-docs` |
