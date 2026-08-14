# PyTorchCompiler/TorchDynamo — Plan

## Goal

Notes on **TorchDynamo**, the bytecode-tracing frontend of `torch.compile`.

## What's here

- [[GraphBreaks]] — causes, finding, reducing graph breaks.
- [[DebugWithTorchDynamo]] — TORCH_LOGS, explain, config flags, minifiers.
- [[Trace-Lifecycle]] — frame → bytecode walk → FX graph.
- [[VariableTracker]] — how Python objects are modeled.
- [[Guards]] — guards and recompilation in depth (the #1 perf gotcha).
- [[SymPy-Symbolic-Shapes]] — the symbolic dims guards reason about.

## What to add next (see also [[../PLAN]])

- [x] Trace lifecycle: frame → bytecode walk → FX graph (link [[../../Programming/python/core/bytecode]])
- [x] `VariableTracker` — how Python objects are modeled
- [x] Guards and recompilation in depth (the #1 perf gotcha)
- [x] `torch._dynamo.explain` deep dive (covered across [[Trace-Lifecycle]] + [[Guards]])
- [x] Interactions with `torch.compiler.disable`/`allow_in_graph` (see [[../Integration-Points]])
