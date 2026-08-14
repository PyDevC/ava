# PyTorchCompiler/TorchDynamo — Plan

## Goal

Notes on **TorchDynamo**, the bytecode-tracing frontend of `torch.compile`.

## What's here

- [GraphBreaks](GraphBreaks.md) — causes, finding, reducing graph breaks.
- [DebugWithTorchDynamo](DebugWithTorchDynamo.md) — TORCH_LOGS, explain, config flags, minifiers.
- [Trace-Lifecycle](Trace-Lifecycle.md) — frame → bytecode walk → FX graph.
- [VariableTracker](VariableTracker.md) — how Python objects are modeled.
- [Guards](Guards.md) — guards and recompilation in depth (the #1 perf gotcha).
- [SymPy-Symbolic-Shapes](SymPy-Symbolic-Shapes.md) — the symbolic dims guards reason about.

## What to add next (see also [PLAN](../PLAN.md))

- [x] Trace lifecycle: frame → bytecode walk → FX graph (link [bytecode](../../../Programming/python/core/bytecode.md))
- [x] `VariableTracker` — how Python objects are modeled
- [x] Guards and recompilation in depth (the #1 perf gotcha)
- [x] `torch._dynamo.explain` deep dive (covered across [Trace-Lifecycle](Trace-Lifecycle.md) + [Guards](Guards.md))
- [x] Interactions with `torch.compiler.disable`/`allow_in_graph` (see [Integration-Points](../Integration-Points.md))
