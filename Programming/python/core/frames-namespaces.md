# Frames, namespaces, and attribute access

The execution model under [bytecode](bytecode.md): every function call creates a **frame** holding its *namespace* (locals), and name resolution walks the **LEGB** chain. The slowness of Python is mostly here — and understanding it is the key to writing fast Python.

## Frames and namespaces

- **Frame** = one execution context: the code object, the `f_locals`/`f_globals`/`f_builtins` dicts, the instruction pointer, and the value stack the bytecode runs on (see [bytecode](bytecode.md)).
- **LEGB** (Local → Enclosing → Global → Builtin): name lookup order. Locals are the fast path; globals and builtins require dict lookups — which is *why* global access is ~2x slower than local.
- Each lookup is a **dict hash**, and attribute access is a dict lookup *plus* a descriptor protocol call — the fundamental Python cost model.

## Why attribute access is slow

`obj.attr` does: type lookup → `__getattribute__` → descriptor `__get__` (maybe) → dict lookup → method-binding. Every framework (PyTorch's `Tensor`!), every ORM, every proxy pays this per access. The practical fixes:

- **`__slots__`**: declare instance attributes up front → instances get a fixed *tuple* of slots instead of `__dict__` → less memory + faster attribute access, and no accidental new attributes (a common PyTorch-subclass footgun, see [conditioning-on-a-python-subclass](conditioning-on-a-python-subclass.md)).
- **Local-variable caching**: `local = self.attr` inside a hot loop avoids repeated attribute dispatch — the single most effective micro-optimization.
- **`lru_cache`**: memoize pure functions instead of recomputing.

## The tracing connection

Dynamo literally *simulates* this model: it walks bytecode, tracks names via `VariableTracker`s, and its guards capture which namespace/attribute facts the traced code depended on (see [VariableTracker](../../../Compilers/PyTorchCompiler/TorchDynamo/VariableTracker.md)). The LEGB chain is exactly what a trace must replicate — that's why global mutation is a recompile trigger (see [Guards](../../../Compilers/PyTorchCompiler/TorchDynamo/Guards.md)).

## Related

- [bytecode](bytecode.md) — the stack machine these namespaces feed.
- [typechecking](typechecking.md) — static checks on top of the dynamic model.
- [python-performance](python-performance.md) — the profiling story.
- [Trace-Lifecycle](../../../Compilers/PyTorchCompiler/TorchDynamo/Trace-Lifecycle.md) — Dynamo's use of all this.
