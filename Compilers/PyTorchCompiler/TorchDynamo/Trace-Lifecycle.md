# Dynamo trace lifecycle

TorchDynamo is a **CPython bytecode-level tracer**: it intercepts a Python frame, walks its bytecode, and builds a graph of the tensor ops it executes. Understanding the lifecycle explains why some things trace and others don't (see [GraphBreaks](GraphBreaks.md)).

## The lifecycle: frame → bytecode walk → FX graph

1. **Frame capture**: when `torch.compile`-wrapped code runs, Dynamo (via a CPython **frame evaluation hook**, `sys.setprofile`-style but at frame granularity) intercepts each Python frame before it executes.
2. **Bytecode walk**: Dynamo executes the frame's bytecode instruction-by-instruction *itself* (an interpreter loop over `dis` instructions, see [bytecode](../../../Programming/python/core/bytecode.md)), tracking what each op does. Tensor ops are recorded; Python logic is *interpreted* with symbolic values.
3. **Graph building**: each torch op becomes a node; the inputs are tracked `VariableTracker`s (see [VariableTracker](VariableTracker.md)). The result is an **FX graph** (`GraphModule`) — the "captured program", now framework IR instead of Python.
4. **Recompile**: graph → `torch._dynamo.config`/optimizers (backward via AOTAutograd, see [AOTAutograd](../AOTAutograd.md)) → backend (Inductor by default).
5. **Guards**: every captured region remembers the assumptions (shapes, dtypes, Python object identities) that made it valid (see [Guards](Guards.md)).

## What traces vs what doesn't

- **Traces**: tensor ops, most `torch.*` calls, control flow it can fold via guards (shape-dependent branches on *known* values).
- **Doesn't** (→ **graph break**): control flow whose value depends on tensor data, Python calls Dynamo can't model, arbitrary Python objects. The frame evaluation hook re-arms, and the rest runs in eager mode (see [GraphBreaks](GraphBreaks.md)).
- The goal: the *whole* frame becomes one graph (one compiled region), not N fragments.

## The config surface that matters

- `torch._dynamo.config.dynamic_shapes`, `recompile_limit`, `suppress_errors`.
- `torch._dynamo.explain(fn)` reports graphs + breaks per graph — the standard "why am I slow" entry point (see [DebugWithTorchDynamo](DebugWithTorchDynamo.md) and the deep dive in [PLAN](../../../Programming/PLAN.md)).

## Related

- [GraphBreaks](GraphBreaks.md) — what interrupts the walk.
- [VariableTracker](VariableTracker.md) — how Python values are modeled during the walk.
- [Guards](Guards.md) — what the walk records about its assumptions.
- [bytecode](../../../Programming/python/core/bytecode.md) — the bytecode being interpreted.
- [AOTAutograd](../AOTAutograd.md) — what happens to the graph *after* capture.
