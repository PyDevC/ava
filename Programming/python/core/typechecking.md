# Typechecking in Python

Python is **dynamically typed** — types are checked at runtime, when values actually meet operators. Typechecking in the Python world therefore means **static type checking**: a separate tool analyzes your code *without running it* and reports type errors, using type annotations.

## The two levels

- **Runtime (dynamic)**: `x + y` fails at runtime if `x` and `y` can't add. Always runs, catches some errors late.
- **Static (ahead of time)**: `mypy file.py` / `pyright file.py` analyzes annotations and control flow, catching errors *before* execution. Catches a whole class of bugs early.

## How it works (annotations)

```python
def add(a: int, b: int) -> int:
    return a + b
```

The checker builds a type lattice, does flow analysis (`if isinstance(...)` narrows the type), and checks assignments/returns against declared types. It doesn't run the code — it models it.

## Main tools

- **mypy** — the original and most popular; slowish but thorough.
- **pyright / pylance** — Microsoft's, fast (node-based), great for IDEs, stricter defaults in places.
- **pyre** — Meta's.
- **basedpyright** — a stricter community fork of pyright.

## Advanced features

- **Generics**: `TypeVar`, `Generic[T]`, `ParamSpec`.
- **Protocols** (structural typing): `Protocol` + `runtime_checkable` — duck typing with checks.
- **Narrowing**: `isinstance`, `Literal`, `TypeGuard` / `TypeIs` (a function that tells the checker "if this returns True, x is narrowed").
- **`cast`, `Any`, `# type: ignore`** — escape hatches when the model isn't expressible.

## Practical notes

- Annotations don't change runtime behavior (mostly — unless `from __future__ import annotations`/`typing` machinery is used; `dataclass`/`pydantic` do use them).
- The point of static typechecking: catch `None` misuse, wrong arg types, refactoring safety, and serve as documentation.
- Related to the bytecode model: types are erased by the time it becomes [[bytecode]]; the checker works on the source/annotation level, so it's a separate discipline.
- See also [[conditioning-on-a-python-subclass]] for runtime type-conditional behavior via subclasses.
