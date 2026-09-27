---
name: resume-project
description: Audits a stale or paused repository, surfaces drift and context gaps, runs a short reentry interview, and produces a Reentry Brief with prioritized first steps. Use when returning to a project after weeks or months, onboarding back to old work, or when the user says retomar, reentrada, proyecto parado, cold start, or resume project.
disable-model-invocation: true
---

# Resume Project

Reentry workflow for a repository that has been idle. **Read-only until the user approves action.**

## Invocation rules

- **User-invoked only** — command, attachment, or explicit mention. Never auto-apply.
- **No writes, commits, installs, or migrations** until the user approves after the Reentry Brief.
- **Facts are the agent's job** — use git, filesystem, and tools. Do not ask the user for info you can look up.
- **Decisions are the user's** — short grilling rounds for intent and priorities only.

## Related skills (delegate, do not duplicate)

| Situation | Delegate to |
| --- | --- |
| No repo standards at all | `setup-repo-standards` |
| One technology needs standards | `extend-domain-standards` |
| Full domain redesign | `grill-with-docs` |
| Exhaustive design tree | `grilling` |

## Process

```
Task Progress:
- [ ] 1. Recon (read-only)
- [ ] 2. Present findings snapshot
- [ ] 3. Short grilling (1–3 rounds max)
- [ ] 4. Deliver Reentry Brief
- [ ] 5. WAIT — user confirms before any action
```

### 1. Recon (read-only)

Gather evidence in parallel where possible:

**Git**
- Current branch, uncommitted changes, stash list
- Last commit date and message (`git log -1`, optionally `-5 --oneline`)
- Stale local branches vs default remote branch
- Open merge/rebase state

**Project health**
- Dependency manifests (`package.json`, `pyproject.toml`, `go.mod`, etc.) — note stack and lockfiles
- README, CONTRIBUTING, `.env.example` vs documented setup steps
- CI config (`.github/workflows`, `azure-pipelines.yml`, etc.) if present
- Test runner config and whether tests exist

**Standards & decisions**
- `.cursor/rules/`, `CODING_STANDARDS.md`, `docs/standards/`, ADRs
- `.cursor/plans/` or similar planning artifacts
- High-signal TODO/FIXME count (sample, not exhaustive grep dump)

**Risk signals**
- Secrets in tracked files (patterns only — do not echo values)
- Large dependency age drift (major versions behind — quick check, not full audit)
- Broken or missing scripts (`npm run`, `make`, etc. from README)

Record gaps as **unknown**, not assumptions.

### 2. Present findings snapshot

Before grilling, show a compact **Snapshot** table in chat:

| Area | Finding |
| --- | --- |
| Last activity | … |
| Git state | … |
| Stack | … |
| Standards present | yes/no — what |
| Obvious risks | … |

### 3. Short grilling (1–3 rounds)

Use the `/grilling` question format but **cap at 3 rounds** and **max 5 questions per round**. Only ask what recon cannot answer.

Each question:

```
❓ **Q1** - **<title>**: <body>

➡️ <recommended answer based on recon>
```

Typical frontier (pick what applies):

- Why paused / what changed externally since last commit?
- Goal for **this session** vs the whole project?
- Which old decisions or branches are still authoritative?
- Blockers or deadlines now?
- Comfort with dependency upgrades vs "just make it run"?

Recompute frontier after each round. Stop when intent for the first session is clear.

### 4. Deliver Reentry Brief

Use [reference-brief-template.md](reference-brief-template.md). Fill every section from recon + grilling answers.

End with:

**Suggested next skills** (only if clearly needed):
- …

**"¿Apruebas que ejecute los primeros pasos del brief?"** — do not act until yes.

### 5. After approval

Execute **only** what the user approved — usually the "First 60–90 minutes" section. One step at a time; report blockers before escalating scope.

If approved work needs standards bootstrap or domain rules, stop and invoke the appropriate skill with user authorization.

## Anti-patterns

- Full `/grilling` session (unbounded design tree)
- Writing rules, ADRs, or refactors without approval
- Big-bang dependency upgrades on reentry
- Asking user for git dates, branch names, or file paths
- Treating stale code as automatically correct

## Additional resources

- Reentry Brief template: [reference-brief-template.md](reference-brief-template.md)
- Worked example: [examples.md](examples.md)
