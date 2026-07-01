# BLANK_BUILD Mode — Detailed Workflow

## When to Use
User wants to build something from scratch. No repo provided.

## Pedagogy: Pair-Programming from Zero

### 1. Goal Confirmation
Restate the goal in one sentence. Confirm or adjust.

> "So you want to build a CLI todo app in Python. Correct?"

### 2. Architecture Sketch (No Code Yet!)
Before any code, sketch the mental model. Use ASCII or bullet list.

```
CLI Todo App
├── commands: add, list, done, delete
├── data store: JSON file on disk
├── entities: Task(id, title, completed, created_at)
└── flow: parse args → route to handler → modify data → save → print result
```

Ask: **"Does this look right to you? Anything you'd change?"** — Get buy-in before coding.

### 3. Skeleton First
Build the empty structure. No logic. Just shapes.

```python
# todo.py
import argparse

def add_task(title: str):
    pass

def list_tasks():
    pass

# etc.

if __name__ == "__main__":
    print("Todo app!")
```

### 4. One Happy Path
Implement the simplest version that works for the most common case.

```python
def add_task(title: str):
    tasks = load_tasks()
    tasks.append({"id": len(tasks)+1, "title": title, "done": False})
    save_tasks(tasks)
    print(f"Added: {title}")
```

**Checkpoint:** "Run it. Does `add` work? What happens if you add two tasks?"

### 5. Edge Cases
- What if the file doesn't exist?
- What if the title is empty?
- What if two tasks have the same title?
- What if the JSON is corrupted?

Add guards one at a time. Each fix is a teaching moment.

### 6. Polish
- Better error messages?
- Color output?
- List formatting?
- Date display?

---

## Handling "I Don't Know Where to Start"
If the user is stuck on the blank page:
1. **Analogize**: "Have you ever made a shopping list? This is a digital version."
2. **Simplify**: "Forget the CLI. How would you store 3 tasks in Python?"
3. **Scaffold**: Offer to write the first line: `def add_task(title):` and ask them to fill it.
4. **Break down**: "Let's just store tasks in a list. We'll worry about saving later."

---

## Toy Example: Todo CLI

**Goal**: Build a command-line todo app in Python.

**After architecture sketch (Step 2):**

```bash
# Step 3: Skeleton
python todo.py add "Buy milk"   # prints "Todo app!"

# Step 4: One happy path
python todo.py add "Buy milk"   # prints "Added: Buy milk"
python todo.py list             # prints "1. Buy milk"

# Step 5: Edge cases
python todo.py add ""           # prints "Title cannot be empty"
rm tasks.json                   # still works, creates new file

# Step 6: Polish
python todo.py list             # shows creation date
python todo.py done 1           # marks item 1 complete
```

After each step: **Checkpoint** — ask the user to predict output, test it, or explain what changed.
