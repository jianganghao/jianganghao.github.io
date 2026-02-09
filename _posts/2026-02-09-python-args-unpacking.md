---
layout: post
title: Python Argument & Unpacking Tricks — A Practical Tutorial
date: 2026-02-09 11:00:00-0400
description: The essential * and ** patterns for arguments, unpacking, and API design
categories: data-science
tags: python, tutorial
giscus_comments: false
related_posts: false
related_publications: true
---

This tutorial covers the most useful Python argument and unpacking patterns: `*`, `**`, `*args`, `**kwargs`, keyword-only, positional-only, and common idioms.

### 1. Positional vs. Keyword Arguments

```python
def f(a, b):
    print(a, b)

f(1, 2)
f(a=1, b=2)
f(1, b=2)
```

```text
positional
keyword
mixed
```

In `def f(a, b)`, parameters are positional-or-keyword by default.

### 2. `*` — Unpack a Sequence into Positional Arguments

```python
def f(a, b):
    print(a, b)

lst = [1, 2]
f(*lst)
```

```text
Equivalent to f(1, 2)
```

Rule: `*sequence` → positional arguments.

### 3. `**` — Unpack a Dict into Keyword Arguments

```python
def f(a, b):
    print(a, b)

d = {"a": 1, "b": 2}
f(**d)
```

```text
Equivalent to f(a=1, b=2)
```

Rule: `**dict` → keyword arguments. Keys must match parameter names.

### 4. `*args` — Collect Extra Positional Arguments

```python
def f(*args):
    print(args)

f(1, 2, 3)
```

```text
(1, 2, 3)
```

Use cases: unknown number of inputs, wrappers/decorators, forwarding arguments.

### 5. `**kwargs` — Collect Extra Keyword Arguments

```python
def f(**kwargs):
    print(kwargs)

f(a=1, b=2)
```

```text
{'a': 1, 'b': 2}
```

Use cases: configuration objects, flexible APIs, passing JSON-like data.

### 6. Forwarding Arguments

```python
def wrapper(*args, **kwargs):
    return real_function(*args, **kwargs)
```

Common in decorators, FastAPI routing, Pydantic models, and tool orchestration.

### 7. `*` in Function Definitions — Keyword-Only Arguments

```python
def f(a, *, b):
    print(a, b)

f(1, b=2)
```

```text
OK
```

```python
f(1, 2)
```

```text
TypeError
```

Use when you want clarity or stable public APIs.

### 8. `/` in Function Definitions — Positional-Only Arguments

```python
def f(a, b, /):
    print(a, b)

f(1, 2)
```

```text
OK
```

```python
f(a=1, b=2)
```

```text
TypeError
```

Common in built-ins and performance-critical APIs.

### 9. Extended Unpacking (Destructuring)

```python
a, *middle, b = [1, 2, 3, 4, 5]
print(a, middle, b)
```

```text
1 [2, 3, 4] 5
```

Useful for flexible slicing and sequence parsing.

### 10. `*` vs. `**` — Common Mistakes

```python
*[1, 2]        # OK
**[1, 2]       # TypeError (list is not a mapping)

*{"a": 1}     # 'a'
**{"a": 1}    # a=1
```

### 11. Why Pydantic and FastAPI Rely on This

```python
data = {"text": "hello", "threshold": "0.7"}
Params(**data)
```

Models expect named fields, so `**data` turns a dict into keyword arguments and triggers validation automatically.

### 12. Mental Model

| Syntax | Meaning |
| --- | --- |
| `*args` | collect positional args |
| `**kwargs` | collect keyword args |
| `*lst` | unpack list (positional) |
| `**dict` | unpack dict (keyword) |
| `*` in `def` | keyword-only |
| `/` in `def` | positional-only |

### 13. One-Sentence Takeaway

Python’s `*` and `**` are the glue that connects lists, dictionaries, JSON, APIs, and function calls.
