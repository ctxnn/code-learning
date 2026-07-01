# Calibration Assessment

## Purpose
Determine the learner's current level in a single, ~5-minute session. Results persist as `user_level` in conversation context.

## Format
Three micro-tasks. Present them all at once. Wait for responses, then score.

---

## Micro-Task 1: Conceptual (Language-agnostic)

**Prompt:**
> Look at this snippet. In one sentence, what does it do?
>
> ```python
> def process(data):
>     seen = set()
>     for item in data:
>         if item not in seen:
>             seen.add(item)
>             yield item
> ```
>
> *If the user is working in TS, offer this equivalent instead:*
> ```typescript
> function* process(data: string[]) {
>   const seen = new Set<string>();
>   for (const item of data) {
>     if (!seen.has(item)) { seen.add(item); yield item; }
>   }
> }
> ```

**What you're looking for:**
- **Novice**: "It goes through the list" or vague description.
- **Junior**: "It filters duplicates" or "returns unique items."
- **Intermediate**: "Lazy deduplication using a generator."
- **Advanced**: "Memory-efficient dedup of an iterable via lazy evaluation. Trade-off: `seen` is unbounded; for bounded memory, we'd need a different approach."

---

## Micro-Task 2: Debugging (Language-specific)

**Python version:**
> ```python
> def add_item(items=[], item="x"):
>     items.append(item)
>     return items
> ```
> What's the bug? When does it manifest?

**TypeScript version:**
> ```typescript
> const user = { name: "Alice" };
> const copy = user;
> copy.name = "Bob";
> console.log(user.name);
> ```
> What does this print? What's the underlying issue?

**What you're looking for:**
- **Novice**: Doesn't identify the issue or gives incorrect answer.
- **Junior**: Identifies the bug (mutable default arg / reference sharing) but not why it happens.
- **Intermediate**: Explains the mechanism (list/dict default evaluated at definition time / object reference vs value).
- **Advanced**: Explains the mechanism AND proposes the fix AND discusses when the pattern is useful vs dangerous.

---

## Micro-Task 3: Small Build

**Prompt:**
> Write a tiny function that counts how many times each word appears in a string. Don't worry about edge cases.
>
> Example: `"the the cat"` → `{"the": 2, "cat": 1}`

**What you're looking for:**
- **Novice**: Struggles to start, or produces non-working code.
- **Junior**: Working solution with `split()` and a dict/map, but maybe inefficient or忽略了 edge cases.
- **Intermediate**: Clean solution with `collections.Counter` or `reduce`, mentions lowercasing or punctuation as follow-up.
- **Advanced**: Concise solution with a generator expression, notes `Counter` from stdlib, and discusses `O(n)` time / `O(k)` space trade-offs or when a trie would be better.

---

## Scoring Rubric

| Level | Criteria |
|-------|----------|
| **Novice** | 0–1 tasks correct. Struggles with basic syntax or concepts. Needs heavy scaffolding. |
| **Junior** | 2 tasks solved correctly. Comfortable with basics. Occasional gaps in deeper understanding. |
| **Intermediate** | All 3 solved with solid reasoning. Can read docs and learn independently. Needs advanced topics. |
| **Advanced** | All 3 solved with clean code + trade-off analysis. Ready for architecture, performance, and deeper patterns. |

## Post-Assessment Action
After scoring, set `user_level` in context and proceed directly to the mode selection:
> "Great! You're at the **[level]** level. Next, what would you like to do?"
> 1. **Share a repo** — I'll teach you how to rebuild it from scratch.
> 2. **Build something new** — I'll pair-program with you step by step.
