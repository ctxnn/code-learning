# code-learning

[![skills.sh](https://skills.sh/b/ctxnn/code-learning)](https://skills.sh/ctxnn/code-learning)

> A personalized coding tutor skill for agent coding environments. Learn any programming language through pair-programming — whether you're dissecting an existing repo or building from scratch.

## What it does

- **Calibrates your level** with a 3-probe assessment
- **Teaches you to rebuild repos** layer by layer (REPO_REPLICA mode)
- **Pair-programs from blank page** with guided checkpoints (BLANK_BUILD mode)
- **Supports any language** — Python & TypeScript have optimized lanes; every other language gets a session-only lane on demand

## Install

```bash
npx skills add ctxnn/code-learning
```

## Quick Start

Trigger the skill and follow the calibration:

```
/skill:code-learning
```

Or simply say: *"I want to learn how to build a REST API in Go"* — the skill will draft a Go lane for the session and start teaching.

## Modes

| Mode | When to use |
|------|-------------|
| **REPO_REPLICA** | You have a repo and want to understand how to build it from scratch |
| **BLANK_BUILD** | You have an idea and want to build it step by step with a pair programmer |

## Language Support

- **Optimized**: Python, TypeScript (pre-built references with idioms, tooling, pitfalls)
- **Dynamic**: Any language via a session-only teaching lane

## Why this skill?

Most coding assistants dump solutions. This one teaches you to *think*:
- Socratic questioning (ask before tell)
- Checkpoints at every step (never leave you hanging)
- Progressive disclosure (hints when stuck, challenge when flying)
- Architecture-first (sketch before code)

## Structure

```
SKILL.md                 # Core orchestrator
references/
  assessment.md          # Calibration probes + rubric
  python.md              # Python-specific teaching guide
  typescript.md          # TypeScript-specific teaching guide
  repo-replica.md        # Detailed REPO_REPLICA workflow
  blank-build.md         # Detailed BLANK_BUILD workflow
assets/
  onboarding.md          # First greeting users see
```

## License

MIT
