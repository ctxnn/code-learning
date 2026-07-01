# code-learning

[![skills.sh](https://skills.sh/b/ctxnn/code-learning)](https://skills.sh/ctxnn/code-learning)

> A personalized coding tutor skill for agent coding environments. Learn any programming language through pair-programming — whether you're dissecting an existing repo, building from scratch, or architecting a complex system.

## What it does

- **Calibrates your level** with a 3-probe assessment
- **Teaches you to rebuild repos** layer by layer (REPO_REPLICA mode)
- **Pair-programs from blank page** with guided checkpoints (BLANK_BUILD mode)
- **Architects complex systems** with milestone planning, ADRs, and testing strategy (COMPLEX_BUILD mode)
- **Supports any language** — Python, TypeScript & Go have optimized lanes; every other language gets a session-only lane on demand
- **Fully self-contained** — works out of the box with no external file dependencies

## Install

```bash
npx skills add ctxnn/code-learning
```

## Quick Start

Trigger the skill and follow the calibration:

```
/skill:code-learning
```

Or simply say: *"I want to learn how to build a REST API in Go"* — the skill will start teaching.

## Modes

| Mode | When to use |
|------|-------------|
| **REPO_REPLICA** | You have a repo and want to understand how to build it from scratch |
| **BLANK_BUILD** | You have an idea for a simple project and want to build it step by step |
| **COMPLEX_BUILD** | You want to build something with databases, APIs, auth, or multi-service architecture |

## Language Support

- **Optimized**: Python, TypeScript, Go (pre-built lanes with idioms, tooling, pitfalls)
- **Dynamic**: Any language via a session-only teaching lane

## Why this skill?

Most coding assistants dump solutions. This one teaches you to *think*:
- Socratic questioning (ask before tell)
- Checkpoints at every step (never leave you hanging)
- Progressive disclosure (hints when stuck, challenge when flying)
- Architecture-first (sketch before code)
- Milestone planning for complex projects (no jumping into code too early)

## Structure

```
SKILL.md                 # Fully self-contained orchestrator (all content inlined)
references/              # Optional enrichment (not required at runtime)
  assessment.md          # Extended calibration probes
  python.md              # Extended Python teaching guide
  typescript.md          # Extended TypeScript teaching guide
  go.md                  # Extended Go teaching guide
  repo-replica.md        # Extended REPO_REPLICA workflow
  blank-build.md         # Extended BLANK_BUILD workflow
  complex-build.md       # Extended COMPLEX_BUILD workflow
assets/
  onboarding.md          # First greeting users see
```

## License

MIT
