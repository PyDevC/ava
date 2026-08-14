# FX graph as IR

The FX graph (`torch.fx`) is the **graph IR that Dynamo outputs** (and that `torch.compile`'s middle-end operates on). It's a functional compute graph: nodes are ops, edges are data flow. Knowing its op set and properties clarifies where Dynamo's work ends and the real compiler's begins.

## The structure

- **`GraphModule`** — a `torch.nn.Module` wrapping a `torch.fx.Graph`: `{node0, node1, ...}` with `Placeholder` (inputs), `call_function` / `call_method` / `call_module` (ops), `get_attr` (parameters/buffers), `output`.
- **`Proxy`** — a symbolic value standing in for a not-yet-existing tensor, so you can write `graph.proxy(x).relu()` in tracing mode and the graph *records* the call.
- **Tracing (symbolic)**: `torch.fx.symbolic_trace` runs the module with `Proxy`s — Python control flow that depends on tensor data breaks, same as Dynamo (this is why Dynamo handles control flow at bytecode level first; FX-trace is the simpler fallback).
- **`torch.export`** produces the **ExportedProgram** — the FX graph with guards + constraints (the "exported" IR going to StableHLO/IREE, see [XLA-StableHLO](../DLCompilers/XLA-StableHLO.md)).

## The op set

FX nodes reference the **actual eager ops** (`torch.add`, `torch.nn.functional.linear`, ...) — it's a *graph-of-eager-ops*, not a lowered IR. Properties:
- **Functional** (mostly): AOTAutograd functionalizes mutations (see [AOTAutograd](AOTAutograd.md)).
- **High-level**: no loops/allocations — just ops on whole tensors.
- This is "graph IR" in the taxonomy of the [IR](../IR/PLAN.md) notes: connectivity + types, not CFG/blocks.

## Why it matters

- It's the *interchange*: Dynamo→FX is the universal frontend; **any** backend (Inductor, XLA via torch-mlir, ONNX export, IREE) consumes FX. `torch.fx` is the API for writing your own transformations (fusion passes, profiling, rewrites) *before* codegen.
- **LazyTensor / compile backends** all share "get an FX graph, do something clever".
- Inspecting: `graph.print_tabular()` gives the node table — the first step in debugging any compile output (see [bytecode](../../Programming/python/core/bytecode.md) for the bytecode view).

## Related

- [Trace-Lifecycle](TorchDynamo/Trace-Lifecycle.md) — how the FX graph is produced.
- [AOTAutograd](AOTAutograd.md) — the FX-level transformation for backward.
- [PLAN](../IR/PLAN.md) — graph-IR vs CFG taxonomy.
- [XLA-StableHLO](../DLCompilers/XLA-StableHLO.md) — where exported FX graphs go.
