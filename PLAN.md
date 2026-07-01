# Plan: Convert System Prompt → General-Purpose Learning Skill

## Context
User has a system prompt (+111 lines, pasted below) that is currently language-specific. Goal: transform it into a **general, language-agnostic learning skill** that can be packaged as a pi skill and published to skills.sh.

---

## Current Prompt (Raw Paste)

**[PASTED AS-IS FROM USER - NOT SHOWN IN PLAN FOR BREVITY]**
> Prompt content: `if user gives you a coding problem, first understand the problem...` (111+ lines)

---

## Goals
1. Convert language-specific instructions to **language-agnostic** ones.
2. Optimize the prompt for **efficiency, clarity, and next-level learning outcomes**.
3. Package as a valid **Agent Skill** per the [Agent Skills spec](https://agentskills.io/specification).
4. Publish to **skills.sh** via `npx skills`.

---

## Approach
- **Phase 1 — Audit prompt** (already done via user paste).
- **Phase 2 — Generalize & Enhance** — rewrite instructions, add meta-cognitive scaffolding, multi-paradigm support, and adaptive difficulty.
- **Phase 3 — Skill Structure** — create directory, `SKILL.md`, and any helper files.
- **Phase 4 — Validate & Package** — test locally, then bundle for `skills.sh`.

---

## Resolved Decisions (from user clarification)

| Question | Decision |
|---|---|
| Target audience | **Anyone learning to code** — no fixed level; entry point is a calibration test |
| Onboarding flow | Always start with a **skill assessment** (2–3 short probes) → infer level (novice / junior / intermediate / advanced) → adapt depth, language vocabulary, and scaffolding |
| Teaching mode | **Two modes**, auto-detected: (A) **Repo Replica** — user pastes an existing repo, skill teaches how to rebuild it from scratch architecture-first; (B) **Blank Page Pair-Programmer** — no repo, step-by-step guided build with hints, checkpoints, and review pauses |
| Code generation policy | **Never dump full solutions by default.** Lead with explanation + small next step. Only produce larger code blocks when user explicitly says "show me" / "give me the code" or after a checkpoint where they tried first. Always explain *why*. |
| Languages | **Python** and **TypeScript** are the optimized lanes (idiomatic patterns, stdlib hints, type-system tips). **Any other language is fully supported via dynamic materialization** — when a user names a language (e.g., Go), the skill creates a fresh `{language}.md` reference file with the same pedagogical scaffold, then teaches in that language as a first-class citizen. |
| Interaction style | **Interactive, multi-turn.** Always end a turn with a checkpoint — a question, a tiny exercise, or an "ask to continue" gate. No single-shot answers. |
| Pedagogy | Socratic + pair-programming hybrid: ask before telling, name concepts when introduced, surface trade-offs, call out common pitfalls, and recap at the end of each chunk. |
| Publishing | GitHub: **ctxnn** (skills.sh publish will use a `ctxnn/code-learning` repo). |

---

## Design

### Onboarding once per session (cache by topic)
Before teaching anything, run **skill-calibration**. Output a short `<assessment>` block with 3 micro-tasks (one conceptual, one debugging, one small-build) tuned to be solvable by an intermediate dev. Map performance to a level and persist it in the conversation context as `user_level:`.

### Skill body (lives in `SKILL.md`)
Use a **state machine** keyed off `mode` and `user_level`:

```
ONBOARD → ASSESS → (REPO_REPLICA | BLANK_BUILD) → LOOP → RECAP
```

**Per-loop contract** (every interactive turn):
1. **Frame** — what's the current micro-goal in the user's terms.
2. **Explain** — the *one* concept needed for this step.
3. **Propose** — smallest actionable next move (code + reasoning).
4. **Checkpoint** — ask the user to run it / answer a question / say "continue".
5. **Adapt** — if user struggles → drop a hint and downgrade scaffolding; if user flies → introduce the next layer (pattern, perf, edge case).

### Modes

**REPO_REPLICA**
1. Ask for repo (paste, paths, or zip summary).
2. Build a `project map`: entry points, modules, data flow, key types, side effects.
3. Teach by **layers from bottom up**: build deps/version → file scaffold → smallest runnable slice → add feature incrementally → match original behavior.
4. After each layer: diff against the original, discuss *why* the original made each choice.

**BLANK_BUILD (pair-programmer)**
1. Restate the user's goal in 1 sentence; confirm.
2. Propose an architecture sketch (entities, flow, modules) before any code.
3. Co-implement step-by-step: skeleton → one happy path → edge cases → polish.
4. At every step: pause for user to type, predict the output, or answer a "what if" question.

### Language lanes & Dynamic Materialization

**Optimized lanes (Python, TypeScript)**
- **Python lane**: typing, stdlib-first, `pytest`, packaging, async awareness.
- **TypeScript lane**: type narrowing, generics, tsconfig, ESM vs CJS, `tsx`/`vitest`.
- These have pre-built `references/python.md` and `references/typescript.md` with idioms, stdlib/tooling notes, and common pitfalls.

**Dynamic materialization (any other language)**
When a user names a new language (e.g., "I want to learn Go"), the skill will:
1. **Materialize** a fresh `references/{language}.md` file using a language-agnostic scaffold (syntax primer, stdlib overview, package manager, build system, testing basics, common idiom categories, gotchas).
2. **Seed** it with AI-generated knowledge for that specific language at the user's level.
3. **Teach** using the same state machine, pedagogical rules, and checkpoint system as Python/TS.
4. **Persist** the generated file so the skill learns and reuses it in future sessions.

Activation phrase: any explicit request like "in Go", "using Rust", "I want to use C++" triggers the materialization wizard before ASSESS.

### Checkouts (recurring)
- **Concept naming**: introduce each new term with a one-line definition and why it exists.
- **Common pitfalls**: end of each chunk lists 2–3 mistakes to watch for.
- **Recap**: at natural breakpoints (after every layer in REPO_REPLICA, every feature in BLANK_BUILD), produce a 4-bullet summary: what we built / what you learned / what's next / try-it-yourself challenge.

---

## Skill Structure

`~/.pi/agent/skills/code-learning/`
```
SKILL.md                 # frontmatter + onboarding + state machine + per-turn contract
references/
  python.md              # Pre-built: idioms, stdlib, tooling, pitfalls
  typescript.md          # Pre-built: types, tsconfig, ESM, testing
  go.md                  # Example of a materialized file (created on first "in Go" request)
  assessment.md          # Calibration probes + rubric
  repo-replica.md        # Detailed REPO_REPLICA workflow + example
  blank-build.md         # Detailed BLANK_BUILD workflow + example
scripts/
  level-detect.py        # Tiny heuristic helper (optional, agent may inline)
assets/
  onboarding.md          # Greeting + first prompt users see
```

Frontmatter (must match spec exactly):
```yaml
---
name: code-learning
description: Personalized coding tutor. Calibrates user level via a short assessment, then teaches by pair-programming — either replicating a pasted repo from scratch or building a new project step by step. Supports Python & TypeScript natively, and any other language on demand. Triggers when the user asks to learn, understand, or build code; shares a repo to study; or wants guided practice.
license: MIT
metadata:
  author: ctxnn
  github: ctxnn
  version: 1.0.0
---
```

---

## Verification
- [ ] Skill loads without validation errors
- [ ] `/skill:code-learning` triggers the skill correctly
- [ ] Test with a sample coding problem in Python and TypeScript
- [ ] Test dynamic materialization: request a new language (e.g., Go), verify `go.md` is created and used
- [ ] Publish to skills.sh and verify installability

---

## Publish Plan
1. Init a public repo `ctxnn/code-learning` matching the local folder layout.
2. `npx skills add ctxnn/code-learning` locally to smoke-test install.
3. Submit to skills.sh index (open PR / follow their current contribution flow at the time of release).
4. README in repo: short pitch, install snippet, a 30-second gif of REPO_REPLICA + BLANK_BUILD.
