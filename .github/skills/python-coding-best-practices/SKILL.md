---
name: python-coding-best-practices
description: Use whenever writing, reviewing, or debugging Python code
---

## Overview

Modern Python development guide (Python 3.10+). Follow these patterns for all Python code in this project.

## Core Principles

1. Use type hints on all function signatures
2. Prefer dataclasses or Pydantic for structured data
3. Use `pathlib.Path` over `os.path`
4. f-strings over `.format()` or `%`
5. Context managers (`with`) for resource management

## Must-Use Patterns

### Type Hints
```python
# Always annotate function signatures
def process(items: list[str], limit: int = 10) -> dict[str, int]:
    return {item: i for i, item in enumerate(items[:limit])}

# Use | for union types (3.10+)
def find(value: str | None) -> str | None: ...
```

### Dataclasses
```python
from dataclasses import dataclass, field

@dataclass
class Config:
    name: str
    tags: list[str] = field(default_factory=list)
    limit: int = 100
```

### Pathlib
```python
from pathlib import Path

# Always
path = Path("data") / "output" / "results.csv"
text = path.read_text()

# Never
import os
path = os.path.join("data", "output", "results.csv")
```

### Error Handling
```python
# Catch specific exceptions, not bare except
try:
    result = fetch_data(url)
except (ConnectionError, TimeoutError) as e:
    logger.error("fetch failed: %s", e)
    raise
```

## Common Mistakes

- Mutable default arguments: `def f(items=[]):` — use `field(default_factory=list)`
- `os.path` instead of `pathlib`
- Bare `except:` instead of specific exception types
- String concatenation in loops instead of `"".join()`
- Checking `type(x) == int` instead of `isinstance(x, int)`

## Quick Reference

| Pattern | Modern way |
|---------|-----------|
| `os.path.join(a, b)` | `Path(a) / b` |
| `open(f).read()` | `Path(f).read_text()` |
| `"hello " + name` | `f"hello {name}"` |
| `dict.get(k, None)` | `dict.get(k)` |
| `type(x) == int` | `isinstance(x, int)` |
