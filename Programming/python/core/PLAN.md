# Programming/python/core — Plan

## Goal of this directory

Cover the **core internals of the Python language** — the execution model and the features that make Python behave the way it does. This matters twice: as a systems user (why is this slow?), and as someone reading/writing the CPython-dependent frameworks like PyTorch (Dynamo literally traces bytecode).

## What's already here

- [[bytecode]] — the stack-machine execution model, opcodes, why Dynamo can trace.
- [[typechecking]] — static type checking, mypy/pyright, generics, narrowing.
- [[conditioning-on-a-python-subclass]] — changing behavior per subclass (isinstance, polymorphism, dispatch).
- [[frames-namespaces]] — frames, LEGB, why attribute access is slow.
- [[gil-threading]] — GIL, threads vs processes.
- [[python-memory]] — refcounting, GC, weakref, leaks.
- [[data-model]] — dunders, descriptors, metaclasses, `__init_subclass__`.
- [[python-c-api]] — the C API, pybind11, ctypes/cffi.
- [[python-performance]] — profiling, `__slots__`, offloading to C/Numpy.

## What to add next

- [x] **Execution model depth**:
  - [x] Frames, namespaces (LEGB), and why attribute access is slow — pairs with [[bytecode]]
  - [x] GIL: what it protects, thread-safety reality, when `multiprocessing` beats threads
  - [x] Memory: refcounting + GC, `weakref`, reference cycles, leaks (matters for long-running training)
- [x] **The data model** (Python's power features):
  - [x] `__dunder__` methods and operator overloading
  - [x] Descriptors, `property`, `__setattr__`, slots
  - [x] Metaclasses and `__init_subclass__` (extends [[conditioning-on-a-python-subclass]])
  - [ ] Iterators/generators/`yield from`, context managers → folded into [[data-model]] (protocols) — add a short generators note if needed
- [x] **Type system depth** (extends [[typechecking]]):
  - [x] `Protocol`/structural typing, `TypeVar`/generics, `Literal` → [[type-system-depth]]
  - [x] `typing` vs `types` — runtime vs static (link to Dynamo's `VariableTracker`) → [[type-system-depth]]
- [x] **Interop**:
  - [x] The C API: `PyObject`, extension modules, `pybind11` (link to [[../../PLAN]] C++ plans)
  - [x] `ctypes` / `cffi` / Cython basics
- [x] **Performance**:
  - [x] When Python is the bottleneck: profiling, `__slots__`, local variable caching, `lru_cache`
  - [x] Specialized/interpreter versions (3.11+ specializing interpreter, 3.13 free-threading) — how they change [[bytecode]]'s model → noted in [[gil-threading]] and [[python-performance]]

## Prioritization

1. **Data model (dunders/descriptors/metaclasses)** — the source of most "magic" in frameworks.
2. **GIL + memory** — the two things that bite production Python.
3. **C API / pybind11** — needed for writing extensions that integrate with PyTorch.
