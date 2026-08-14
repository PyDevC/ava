# Programming/python/core — Plan

## Goal of this directory

Cover the **core internals of the Python language** — the execution model and the features that make Python behave the way it does. This matters twice: as a systems user (why is this slow?), and as someone reading/writing the CPython-dependent frameworks like PyTorch (Dynamo literally traces bytecode).

## What's already here

- [[bytecode]] — the stack-machine execution model, opcodes, why Dynamo can trace.
- [[typechecking]] — static type checking, mypy/pyright, generics, narrowing.
- [[conditioning-on-a-python-subclass]] — changing behavior per subclass (isinstance, polymorphism, dispatch).

## What to add next

- [ ] **Execution model depth**:
  - [ ] Frames, namespaces (LEGB), and why attribute access is slow — pairs with [[bytecode]]
  - [ ] GIL: what it protects, thread-safety reality, when `multiprocessing` beats threads
  - [ ] Memory: refcounting + GC, `weakref`, reference cycles, leaks (matters for long-running training)
- [ ] **The data model** (Python's power features):
  - [ ] `__dunder__` methods and operator overloading
  - [ ] Descriptors, `property`, `__setattr__`, slots
  - [ ] Metaclasses and `__init_subclass__` (extends [[conditioning-on-a-python-subclass]])
  - [ ] Iterators/generators/`yield from`, context managers
- [ ] **Type system depth** (extends [[typechecking]]):
  - [ ] `Protocol`/structural typing, `TypeVar`/generics, `Literal`
  - [ ] `typing` vs `types` — runtime vs static (link to Dynamo's `VariableTracker`)
- [ ] **Interop**:
  - [ ] The C API: `PyObject`, extension modules, `pybind11` (link to [[../../PLAN]] C++ plans)
  - [ ] `ctypes` / `cffi` / Cython basics
- [ ] **Performance**:
  - [ ] When Python is the bottleneck: profiling, `__slots__`, local variable caching, `lru_cache`
  - [ ] Specialized/interpreter versions (3.11+ specializing interpreter, 3.13 free-threading) — how they change [[bytecode]]'s model

## Prioritization

1. **Data model (dunders/descriptors/metaclasses)** — the source of most "magic" in frameworks.
2. **GIL + memory** — the two things that bite production Python.
3. **C API / pybind11** — needed for writing extensions that integrate with PyTorch.
