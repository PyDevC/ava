# PyTorchCompiler/TorchDynamo — Plan

## Goal

Notes on **TorchDynamo**, the bytecode-tracing frontend of `torch.compile`.

## What's here

- [[GraphBreaks]] — causes, finding, reducing graph breaks.
- [[DebugWithTorchDynamo]] — TORCH_LOGS, explain, config flags, minifiers.

## What to add next (see also [[../PLAN]])

- [ ] Trace lifecycle: frame → bytecode walk → FX graph (link [[../../Programming/python/core/bytecode]])
- [ ] `VariableTracker` — how Python objects are modeled
- [ ] Guards and recompilation in depth (the #1 perf gotcha)
- [ ] `torch._dynamo.explain` deep dive
- [ ] Interactions with `torch.compiler.disable`/`allow_in_graph`
