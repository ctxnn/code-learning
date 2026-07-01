# Python Teaching Reference

Quick reference for the Python lane. Loaded when the user is working in Python.

---

## Idioms

### List / Dict Comprehensions
```python
# Prefer comprehensions over loops
squares = [x**2 for x in range(10)]           # list
name_map = {u.id: u.name for u in users}      # dict
```

### Generators & Context Managers
```python
# Lazy evaluation for large iterables
lines = (line.strip() for line in huge_file)

# contextmanager for resources
with open("data.txt") as f:
    data = f.read()
```

### Decorators
```python
from functools import wraps

def log_calls(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        print(f"Calling {fn.__name__}")
        return fn(*args, **kwargs)
    return wrapper
```

### Dataclasses
```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    x: float
    y: float
```

---

## stdlib Modules to Know

| Module | Use Case |
|--------|----------|
| `pathlib` | Modern path handling (replace `os.path`) |
| `collections` | `Counter`, `deque`, `defaultdict`, `namedtuple` |
| `itertools` | Efficient iterators: `chain`, `groupby`, `combinations` |
| `functools` | `lru_cache`, `partial`, `wraps` |
| `json` | Serialization (always set `default=str` for custom objects) |
| `re` | Regex; prefer `re.compile` for repeated patterns |
| `typing` | Type hints; prefer `from typing import ...` |

---

## Tooling

| Tool | Purpose |
|------|---------|
| `pytest` | Testing with fixtures, parametrize, and assert rewriting |
| `mypy` / `ruff` | Type checking and linting |
| `virtualenv` / `venv` | Environment isolation |
| `poetry` / `uv` | Dependency management and packaging |

---

## Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| Mutable default args | `def f(x=[])` — `[]` evaluated once at definition | `def f(x=None): x = x or []` |
| Late binding closures | `lambda` in loop captures last value | Use `default` arg: `lambda x, i=i: ...` |
| `is` vs `==` | `is` checks identity, not equality | Use `==` for value comparison |
| GIL confusion | "Python has no real parallelism" — GIL limits threads for CPU-bound work | Use `multiprocessing` or `asyncio` for concurrency |
| `except Exception` swallowing | Catches too much, masks `KeyboardInterrupt` | Be specific; use `except TypeError` |

---

## Quick Snippets

**File reading with error handling:**
```python
from pathlib import Path

def read_lines(path: Path) -> list[str]:
    try:
        return path.read_text().splitlines()
    except FileNotFoundError:
        return []
```

**Counter for frequency:**
```python
from collections import Counter

def word_counts(text: str) -> dict[str, int]:
    return Counter(text.lower().split())
```
