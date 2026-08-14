# SymPy symbolic shapes

`torch.compile` reasons about tensor shapes **symbolically**: a shape `(batch, 128)` during tracing is `(s0, 128)` where `s0` is a **symint** — a symbolic integer (SymPy expression) representing a dimension that's unknown-but-consistent. This is the machinery behind dynamic shapes and guards.

## What symints are

- During tracing, `tensor.size(0)` returns a `torch.SymInt`, not a Python int. All shape arithmetic (`x.size(0) * 2`, `reshape`, `broadcast`) produces SymPy expressions: `s0*2`, `s0+s1`, `max(s0, s1)`.
- Symbolic dims get **concrete values at runtime**; the compiled code checks guards (see [Guards](Guards.md)) to confirm the concrete value matches the symbolic constraints the compile assumed.
- `torch._dynamo.config.dynamic_shapes` / `torch.export`'s `dynamic_shapes=` argument turn *specific* dims into symints; with `dynamic_shapes=False`, every dim is specialized (constant) — fast, but recompiles per shape.

## Why symbolic is the right model

- **One compile for a range of shapes**: guards on `s0 == s1` (constraint) instead of `s0 == 128 && s1 == 128` (values). Batch-size flexibility without recompile storms.
- **Proving**: SymPy lets Inductor *prove* facts needed for correctness/optimization — e.g. `if s0 * 2 == x.numel():` folds symbolically, deciding buffer sizes, `stride` legality, and whether a `view` is valid, without runtime branches.
- **The trade**: symbolic codegen is *harder* (loops over symbolic bounds, indexing with `s0*i`), and over-dynamic shapes (everything a symint) can *slow down* compile output. The `dynamic_shapes=True` on the *right* dims is the skill.

## How to observe it

- `TORCH_LOGS=graph` / `torch._dynamo.explain` shows `SymInt`-typed shapes in the graph.
- `torch.export` (or `torch._dynamo.mark_dynamic`) is where you explicitly say which dims are dynamic — then Inductor sees symints for those and constants for the rest.

## Related

- [Guards](Guards.md) — symints are *what* guards check.
- [Trace-Lifecycle](Trace-Lifecycle.md) — where the symbolic shapes are born.
- [bytecode](../../../Programming/python/core/bytecode.md) — the bytecode view of the same trace.
- [XLA-StableHLO](../../DLCompilers/XLA-StableHLO.md) — XLA's equivalent shape-polymorphism model.
