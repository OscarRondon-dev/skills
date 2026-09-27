---
name: proto-lab
description: Conversation-first lab around throwaway prototypes. Proposes 2-3 experiment directions, locks a short contract with one grill round, then builds and iterates by delegating to the prototype skill. Use when the user invokes proto-lab, wants to talk through a prototype before building, mix ideas across options, or keep iterating an artifact with professional pushback instead of jumping straight to /prototype.
disable-model-invocation: true
---

# Proto Lab

A **lab** around [prototype](../prototype/SKILL.md). Prototype is the build engine. This skill is the director: talk, propose, lock, build, iterate.

Do not reimplement LOGIC or UI generation. After the contract is locked, **read** [prototype SKILL.md](../prototype/SKILL.md) and then [LOGIC.md](../prototype/LOGIC.md) or [UI.md](../prototype/UI.md) according to the locked branch, and follow those files for the entire build.

Do not run the full [grilling](../grilling/SKILL.md) tree. The grill here is only the **contract** of the experiment.

Match the user's language.

## Stance — propositivo

Think. Do not just comply.

- Arrive with a recommendation, not a blank interview.
- If the user likes option 1 with something from option 2, **synthesize a better third**. Do not glue. Say so when a mix is incoherent.
- Do not reinvent the wheel. If a known pattern exists, say **it is better this way, because…**. When unsure, look it up (web, similar products, the surrounding codebase) and come back with a proposal — never with “what do you prefer?”
- **Structure, state model, known patterns:** hold the line.
- **Visual taste, copy, vibe:** offer one professional alternative, then follow the user.
- Facts (code, files, existing patterns) are your job. Decisions are the user's — after you have proposed.

## Contract

The experiment is locked when all four are explicit:

1. **Question** the prototype answers (one sentence)
2. **Branch** — LOGIC or UI (prototype's split; getting this wrong wastes the artifact)
3. **3 cases** someone can play or see
4. **Out of scope**

Print the contract whenever it changes.

## Phases

Skip ahead if the session already has a locked contract and an artifact — go to **Iterate**.

### 1. Listen

Up to 1–2 turns of free conversation. If the user already dumped enough, skip.

Done when you can draft 2–3 distinct experiments, or you know which fact to look up first.

### 2. Propose

Research first when the domain has established patterns — short, then propose. Do not stall.

Offer **2–3 paths**, one marked **recommended**. Each path is already a full contract (question, branch, 3 cases, out of scope). Paths must be actually different (different question, branch, or cases) — not three wordings of the same demo.

Done when the user has picked, mixed, or rejected. Then synthesize. If the mix is weak, say why and offer the better third.

### 3. Short grill

One round, **3–5 questions**, only what is still ambiguous in the contract. Format:

```
❓ **Q1** - **<title>**: <body>

➡️ <your recommended answer>
```

Wait for answers. Second round only if the contract is still ambiguous. Never grill the whole product.

Done when you restate the locked contract and the user has not contradicted it. **Do not build before that.**

### 4. Build

Read and follow prototype for the locked branch. Same rules: throwaway and named as such, trivial to run, no persistence by default, skip polish, surface full relevant state.

If there is no project: LOGIC is a double-click HTML; UI needs a host app — say so and either use a scratch app or challenge whether the real question is LOGIC. Do not invent a fake UI app just to have somewhere to put pixels.

Place the artifact next to what it is prototyping when a project exists.

Done when the user can run it and the contract's 3 cases are playable or visible. Restate the contract at the top of the artifact (prototype already requires this).

### 5. Iterate

Every turn: propose the **next delta** (what changes, why it is better). Never ask “what else do you want?”

- **Mutate the same artifact** while the question is the same and the artifact can still show it.
- **New artifact** when you would be answering a different question, the LOGIC/UI branch was wrong, or the current artifact cannot show what must be seen. Propose the switch out loud, name the old one discarded, lock a fresh contract (short grill only if the new contract is ambiguous), then build. Do not silently restart.

Capture (prototype rule 6: throwaway branch, verdict) only when the **question is settled** and the user is done iterating. Until then the lab stays live.

## Mutate vs new

| Signal | Move |
| --- | --- |
| Same question, “like this but change X” | Mutate |
| Mashup of variants / cases | Mutate, after synthesizing |
| Question changed | New artifact |
| Wrong branch (LOGIC ↔ UI) | New artifact |
| Artifact cannot show the cases | New artifact |

## Examples

**New logic idea, no repo yet.** User: “quiero probar si un pedido puede quedar a medias”. Propose 3 contracts (partial-pay machine vs two aggregates vs a simple status enum — recommend the machine). Short grill on cases. Build one HTML via LOGIC.md. Later: “el 1 pero con el pago del 2” → synthesize, mutate the same file, do not start over.

**UI, already in an app.** User: “no sé cómo debe verse el inbox”. Propose 3 visual directions on the existing route (prototype UI sub-shape A). User picks density from A and hierarchy from B. Do not splice both: propose a third layout and say why. Grill only leftover contract holes, then follow UI.md.

## Anti-patterns

- Jumping to prototype before a locked contract
- Full product grilling
- Duplicating LOGIC.md / UI.md in this skill
- Executing a weak mashup to please
- Inventing a pattern that already exists
- Capturing / folding into production mid-lab
