---
name: ticket-loop
description: >-
  Orchestrates named ticket-loop multitask sessions: recap, parent-proposed
  TDD seams, full paste of implement/code-review/tdd into each child, one
  child per ticket with two-axis grandchild review, then gate and resume of
  the same child. Use when the user names ticket-loop.
disable-model-invocation: true
---

# Ticket Loop

Parent-only orchestrator for a **multitask** session. Do not run this skill
unless the user **explicitly names** ticket-loop.

Talk to the user in the user's language. This file is English.

This skill is the parent loop and the child contract. Do not rewrite
implement, code-review, or tdd. Paste those files into the child, then apply
the **overrides** below so pasted skills cannot send the child back to the
user.

Terms: **parent**, **child**, **grandchild**, **recap**, **gate**, **resume**,
**seam**, **ticket**, **ticket candidate**, **practice tip**.

## Roles

| Role | Who | Job |
|------|-----|-----|
| **parent** | this agent | recap, seams, practice tips, launch, gate, resume, closing table, surface ticket candidates |
| **child** | one Task per **ticket** | implement → two-axis review → apply fixes → report |
| **grandchild** | two Tasks spawned by the **child** | one axis each: Standards, Spec |

## Recap — do not launch until the user confirms

Ask all seven. Never assume. Materialize each spec (fetch URL / issue / file)
**before** proposing seams or practice tips. Do not launch a child before
confirmation.

1. **Spec/source of each ticket** — URL, #id, pasted text, or docs path.
   Never invent a spec. After the user answers, resolve it to text the child
   can use. An unresolved spec is not confirmation.
2. **Parallel vs serial** for this session — always ask.
3. **Model** — default **composer-2.5-fast**. Upgrade only if (a) the spec is
   vague or contradictory, (b) the work spans several unknown areas, or
   (c) the user asks.
4. **Current git branch** — show it (`git branch --show-current`). The child
   must never checkout, switch, or create a branch. If the branch is wrong,
   do not launch. Wait for the user to change it or to authorize a change
   explicitly in the current message.
5. **TDD seams** — the **parent** proposes 0–N seams from the materialized
   spec. The child never proposes seams on the happy path. The user
   confirms, edits, or says none. Parent-confirmed seams **are** tdd's
   "confirm with the user"; the child must not re-ask.
6. **Three skills pasted in full** into every child prompt — confirm these
   files will be Read and pasted (entire `SKILL.md`, including frontmatter):
   - `~/.cursor/skills/implement/SKILL.md`
   - `~/.cursor/skills/code-review/SKILL.md`
   - `~/.cursor/skills/tdd/SKILL.md`
7. **Practice tips** — after the spec is materialized, the **parent** looks
   at likely hosts for this ticket, reads the repo standards **index** if
   it exists, and opens **only** the satellite that applies. Propose **0–5**
   tips. Each tip: host + one action for **this** slice. The user confirms,
   edits, or says none. Do not paste the standards book. Do not recite
   line-count lint or “write clean code”. If nothing in the standards
   applies, propose none — do not invent.

```
Recap
- [ ] spec/source per ticket (never assumed; materialized to text)
- [ ] parallel vs serial
- [ ] model (default composer-2.5-fast)
- [ ] current git branch shown; child must not switch
- [ ] TDD seams proposed by parent; user confirmed / edited / none
- [ ] three skills will be pasted in full
- [ ] practice tips proposed by parent; user confirmed / edited / none
```

## Workspace

Before launch, the parent must be in the **target repo root**. If still in
home or an empty workspace, call `move_agent_to_root` (or equivalent) first.
Do not launch children from home/empty.

## Skill injection

Subagents do **not** inherit named skills. Putting `/implement`,
`/code-review`, or `/tdd` in the prompt is **forbidden as the only injection**.

For every child, **Read** each of the three `SKILL.md` files and **paste the
full file contents**. Optional: also list the three paths. Paste is
mandatory; paths or names alone are not enough.

## Overrides (pasted skills vs this loop)

The child follows pasted skills **except** where they would talk to the user
or skip a required step. Put this block in every child prompt:

- Pasted file contents **are** the skills. Do not invoke them by name.
- **Seams:** execute only the AGREED TDD SEAMS. Do not ask the user. Do not
  invent seams. Parent confirmation already satisfies tdd.
- **Practice tips:** execute only the AGREED PRACTICE TIPS. Do not invent
  extra style work. Do not skip an agreed tip without saying why in the
  report.
- **Fixed point:** `start SHA` from `git rev-parse HEAD` taken **before any
  code change**. Pass that SHA into both grandchildren. Do not ask. Do not
  default to `main`.
- **Spec axis:** the SPEC in this prompt is the spec. Pass it to the Spec
  grandchild. Never skip Spec. Do not run `setup-matt-pocock-skills`. Do not
  ask where the spec is.
- **After implement:** run the pasted code-review workflow yourself: spawn
  **two grandchildren in parallel** (Standards + Spec). Do not return
  implement-only. The parent must never spawn a review agent.
- **Commit:** implement wins — commit implement work and review fixes on the
  current branch.
- **Review fixes:** apply findings **in scope of this ticket** (acceptance
  criteria still failing; creep this child introduced; hard standard
  breaches). Do not apply judgement smells that need a new seam or another
  ticket — **except** the two overrides below. Do not implement out-of-scope
  work.
- **Hard ESLint on touched files** (e.g. `max-lines`, `max-lines-per-function`):
  fix in-scope; not a ticket candidate.
- **Divergent Change introduced or worsened** in touched hosts
  (pages/handlers): extract the concern in-scope unless the spec/AC
  explicitly defers composition. Pre-existing debt in **untouched** files
  may stay a ticket candidate.
- **Verify before done:** run the repo's documented verify belt (see
  AGENTS.md, README, or `.cursor/rules/*verify*`). Pick the narrowest belt
  that covers every path this diff touches. Report the command, base ref,
  and feature paths touched. Do not claim done on red.
- **Verify output:** read the **full** stdout/stderr of verify/lint. Do
  **not** pipe to `tail`, `head`, `Select-Object -Last`, or grep-only
  filters — ESLint/typecheck errors appear **above** the summary line.
  On failure, paste the failing step's error lines in the child report.
- **Ticket candidates:** demonstrable leftover (missed caller of this
  contract, missing agreed-seam coverage, real hole that is not this
  slice; pre-existing debt in untouched files). One line: what, why not
  this ticket, what a new ticket would deliver. Not Fowler nits. Not
  “would be nice”.
- **Never** run `/to-tickets`, quiz the user, or publish issues.
- **Questions:** report to the parent. Never talk to the user.

## Parent launch checklist

```
Launch
- [ ] recap confirmed
- [ ] in target repo root
- [ ] branch is the one the user wants
- [ ] overlapping files: warned if parallel
- [ ] one child per ticket
- [ ] three SKILL.md files pasted in full
- [ ] overrides block included
- [ ] agreed seams listed (or "none")
- [ ] agreed practice tips listed (or "none")
- [ ] materialized spec in the prompt
- [ ] model set (default composer-2.5-fast)
```

## Child contract (one child per ticket)

1. Capture `git rev-parse HEAD` **before any code change** (start SHA).
2. Stay on the current branch. No checkout, switch, or create.
3. Execute only the **agreed** TDD seams. Do not invent seams.
4. Execute only the **agreed** practice tips. Do not invent extra ones.
5. Follow pasted **implement** (including **commit**).
6. After implement, follow pasted **code-review** under the overrides: spawn
   two grandchildren in parallel; pass start SHA + spec into both; record
   both agent ids.
7. Apply **in-scope** review findings and **commit** those fixes. List
   leftover demonstrable debt as **ticket candidates**. Do not run
   `/to-tickets`.
8. Run the repo verify belt per overrides. Fix red before reporting done.
   Capture **full** verify/lint output — never truncate with `-Last` /
   `tail` / `head` before reading error lines.
9. If blocked because a **new** seam is required: stop and return to the
   parent (escape hatch). Do not invent TDD and continue.
10. Never talk to the user.
11. Do not return after implement-only.

## Child-prompt skeleton

```
You are the child for one ticket. Do not talk to the user; report to the parent.

TICKET: <id or title>
SPEC (materialized; this IS the spec for code-review):
<full spec text>

AGREED TDD SEAMS (execute only these; do not invent; do not re-ask):
<list, or "none">

AGREED PRACTICE TIPS (execute only these; do not invent extra style work):
<list, or "none">

GIT:
- Stay on the current branch. No checkout, switch, or create.
- Before ANY code change, capture start SHA: git rev-parse HEAD
- Pass that SHA to both grandchildren as the code-review fixed point.

OVERRIDES:
- Pasted file contents are the skills. Do not invoke by name.
- Seams: AGREED TDD SEAMS only. Parent already confirmed them.
- Practice tips: AGREED PRACTICE TIPS only. Do not invent extra ones.
- Spec axis: never skip. Do not run setup-matt-pocock-skills. Do not ask.
- After implement: spawn two grandchildren in parallel (Standards + Spec).
  Pass start SHA + SPEC into both. Record both agent ids.
- Commit implement work and in-scope review fixes on the current branch.
- Do not apply judgement smells that need a new seam or another ticket,
  except: hard ESLint on touched files → fix; Divergent Change introduced
  or worsened in touched hosts → extract unless AC defers.
- Run repo verify belt before done (narrowest belt covering the diff).
  Report command, base ref, feature paths touched. Read full verify/lint
  output — no tail / Select-Object -Last / head; paste error lines if red.
- Ticket candidates: demonstrable leftover only. Never /to-tickets.
- If a NEW seam is required: stop and return. Do not invent TDD.
- Do not return after implement-only.

===== PASTE implement (full SKILL.md) =====
<PASTE implement>
===== end implement =====

===== PASTE tdd (full SKILL.md) =====
<PASTE tdd>
===== end tdd =====

===== PASTE code-review (full SKILL.md) =====
<PASTE code-review>
===== end code-review =====

Optional re-read paths:
- ~/.cursor/skills/implement/SKILL.md
- ~/.cursor/skills/code-review/SKILL.md
- ~/.cursor/skills/tdd/SKILL.md

Return exactly this report:

## Child report
- spec used:
- start SHA:
- end SHA:
- commit list:
- verify command:
- verify base ref:
- verify result: pass | fail
- verify failure excerpt: (error lines from failing step, or "n/a")
- feature paths touched:
- grandchild agent id (Standards):
- grandchild agent id (Spec):

## Standards
<verbatim report from the Standards grandchild>

## Spec
<verbatim report from the Spec grandchild>

- fixes applied:
- practice tips applied: (or "none")
- anything out of scope:

## Ticket candidates
- <what was seen> — why not this ticket — what a new ticket would deliver
- (or "none")
```

## Ticket candidates (not `/to-tickets`)

**Out of scope** = other agreed slice (later ticket, FE, ops). Leave it.

**Ticket candidate** = real hole found on the way that this ticket must
not absorb. The child lists it. The child does **not** implement it, does
**not** talk to the user, does **not** publish.

Parent shows candidates after each gate pass and again with the closing
table. Wait for the user. Only if they pick items, run `/to-tickets` in
**this** parent chat (quiz + publish). Never spawn a child to invent a
backlog.

## Parent gate

Fail (then **resume the same child**) if any of:

- missing implement
- missing either axis report (`## Standards` or `## Spec`)
- missing grandchild ids
- missing commits
- missing verify command / base ref / feature paths touched
- verify not run or reported red without explanation
- skipped agreed seams without explanation
- skipped agreed practice tips without explanation
- dirty tree after claiming done
- Spec axis says "no spec available" (parent already materialized the spec)

The parent does **not** re-implement or re-run code-review itself.

On gate fail, **resume the same child id** with what is missing. Never launch
a **new** agent to finish the review or apply leftover fixes.

```
Gate
- [ ] implement happened
- [ ] start SHA and end SHA present
- [ ] commits present (implement + review fixes as needed)
- [ ] verify command, base ref, and feature paths touched reported
- [ ] verify passed (or failure explained)
- [ ] agreed seams executed or explained
- [ ] agreed practice tips executed or explained
- [ ] ## Standards verbatim + Standards grandchild id
- [ ] ## Spec verbatim + Spec grandchild id
- [ ] Spec axis did not skip for missing spec
- [ ] tree not left dirty after claiming done
```

Pass → ticket done. Show **ticket candidates** (or none). Do not start
`/to-tickets` until the user picks. Fail → resume that child. Do not start
the next ticket (serial) or declare the batch done (parallel) until the
gate passes. Missing candidates section is not a gate fail; treat as none.

## Resume skeleton

```
Resume the same child. Do not start a new agent.

Gate failed because: <missing ids / missing axis / no commit / skipped seams / …>
Do the missing step. Keep start SHA <sha>. Stay on the current branch.
Still do not talk to the user. Return the full child report.
```

## Dead child

If resume is impossible, tell the user. A replacement child only if the user
authorizes, and only with the prior report/diff handed off.

## Parallel vs serial

- **Serial**: next ticket only after the current child passes the gate.
- **Parallel**: one child per ticket; gate each as it returns; resume that
  same child on failure; do not declare the batch done until every ticket
  is gated.
- If the user chose parallel and tickets overlap files: **warn** and
  recommend those overlapping tickets go serial.

## Closing table (after the batch)

| ticket | start SHA | end SHA | commits | two-axis review? | fixes? | out of scope | ticket candidates |
|--------|-----------|---------|---------|------------------|--------|--------------|-------------------|

After the table, list every candidate from the batch. Ask which (if any)
should become tickets via `/to-tickets`. Do not publish until the user
answers.

## Anti-patterns

- Passing only skill names (or only paths) to a child
- Parent launching a new agent for code-review
- Child offering or inventing seams on the happy path
- Child asking the user for seams, fixed point, or spec
- Skipping the Spec axis because code-review said "no spec"
- Switching git branch without explicit user authorization in the current
  message
- Skipping the recap
- Dumping generic “clean code” or line-count tips instead of 0–5 host+action tips
- Ignoring a child's return and starting the next thing without the gate
- Child running `/to-tickets` or opening issues
- Dumping a real hole only under "out of scope" with no ticket candidate
- Implementing leftover debt "while we're here"
- Truncating verify/lint output (`tail`, `head`, `Select-Object -Last`,
  grep-only) so ESLint/typecheck errors above the summary are never read
