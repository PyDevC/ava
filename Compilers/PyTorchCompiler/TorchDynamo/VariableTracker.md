# VariableTracker

`VariableTracker` is how Dynamo **models Python objects while tracing** — the bridge between "Python value" and "symbolic graph value". When Dynamo interprets bytecode, every Python variable (`x`, `self`, `config.hidden_dim`) is a `VariableTracker` carrying *what Dynamo knows* about it.

## What a tracker holds

A tracker wraps:
- **The concrete value** (for constants: an int, a list, a string).
- **A symbolic "graph value"** for tensors: the FX node that produces it, plus metadata (dtype, shape/symints, layout, requires_grad).
- **Type + provenance**: is it a tensor? a dict? a module? where did it come from?

So `x` in the frame is represented by `TensorVariable(x, node=n0, dtype=fp32, size=[s0,s1])` — the graph sees `n0`; the interpreter sees `x`.

## The two ways a tracker resolves

1. **Specialization** (the common case): Dynamo knows the concrete value, so it inlines it. A `PythonVariable` with a known constant → the branch folds → no graph break. This is why *constant* shapes/loop bounds compile cleanly.
2. **Graph break / dynamic**: unknown Python object → can't model → break (eager fallback); unknown-but-tensor data → **guarded** (record the constraint, see [Guards](Guards.md)).

## Why it matters

- **Performance is decided by tracker fidelity**: the more Python objects Dynamo can *symbolically model* (attributes, indexing, dict/method access on known types), the fewer breaks. Custom Python classes whose attributes Dynamo can't follow are the #1 source of surprising breaks (see [conditioning-on-a-python-subclass](../../../Programming/python/core/conditioning-on-a-python-subclass.md) for the `__instancecheck__` story).
- `TORCH_LOGS=dynamo` shows tracker decisions per instruction — the practical way to see "why did this break".

## Related

- [Trace-Lifecycle](Trace-Lifecycle.md) — where trackers are created and consumed.
- [Guards](Guards.md) — the constraints emitted when a tracker *can't* fully specialize.
- [GraphBreaks](GraphBreaks.md) — what happens when no tracker covers an op.
- [conditioning-on-a-python-subclass](../../../Programming/python/core/conditioning-on-a-python-subclass.md) — an example of tracker limits.
