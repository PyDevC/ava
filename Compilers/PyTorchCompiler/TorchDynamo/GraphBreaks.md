# Graph Breaks

TorchDynamo traces Python bytecode and turns it into an FX graph to hand to a backend (like [TorchInductor](../TorchInductor/EntryPoint.md)). When it hits something it can't faithfully trace, it has to **stop tracing, emit the graph compiled so far, and resume tracing later** — that split is a *graph break*. Everything inside a single trace becomes one fused kernel region; a graph break means more kernels, more Python↔C++ overhead, less fusion.

## What causes graph breaks

The most common sources:

- **Data-dependent control flow** — `if x.sum() > 0` or loops with conditions on tensor values. Dynamo can't know which branch runs at trace time, so it breaks. (Note: `if` on a plain Python `bool` from a Python scalar is fine; the problem is when the condition depends on tensor data.)
- **Calling arbitrary C/CUDA extensions** or ops Dynamo doesn't know — it can't see inside them to build the graph.
- **Python I/O and unsupported Python constructs** — `print`, file writes, list comprehensions over tensors in weird ways, exotic `__dunder__` usage.
- **Not-fully-supported torch APIs** (older `torch` versions missing FX/symbolic-support for a given op).
- **Large `if`/`elif` chains over shape-dependent conditions.**

Graph breaks on iteration over tensors (`for x in tensor`) are also common because a Python `for` over a tensor slices per-row — if the backend can't fuse that, Dynamo breaks.

## How to find them

```bash
TORCH_LOGS=dynamo python my_script.py
```

Look for lines with `[graph break]`. Also useful:

```python
from torch._dynamo.explain import explain
explain_break_count = explain(fn, *args)
```

This prints a summary with the number of graph breaks and the reasons for the first N breaks.

## How to reduce them

- Rewrite data-dependent control flow into vectorized ops (`torch.where`, masking) so Dynamo never needs to branch.
- Use `torch.compile(fullgraph=True)` to turn every break into a hard error during development, so you find them immediately.
- Mark unsupported-but-safe functions with `torch.compiler.allow_in_graph(fn)` (Dynamo will inline them) — use sparingly; it can silently change semantics.
- Hide awkward code behind `torch.compiler.disable(fn)` so the function runs eagerly and only the surrounding region is compiled.

## Why you should care

Every graph break means the compiler can't see the whole computation, so it loses fusion, shape/propagation, and low-precision opportunities. The golden rule: **fewer, bigger graphs → faster code**. See also [DebugWithTorchDynamo](DebugWithTorchDynamo.md).
