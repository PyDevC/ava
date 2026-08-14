# PyTorchCompiler — Plan

## Goal of this directory

Understand PyTorch's **compiler stack** (`torch.compile`): how TorchDynamo traces Python into a graph, how AOTAutograd handles autograd, and how TorchInductor lowers that graph to fast Triton/C++ code. This is the most relevant compiler work to me (I use PyTorch daily), and it ties together [PLAN](../PLAN.md)'s DL-compiler theory with PyTorch's internals (see [PLAN](../../PyTorch/PLAN.md)).

## What's already here

- **TorchDynamo/GraphBreaks.md** — what causes graph breaks, how to find/reduce them.
- **TorchDynamo/DebugWithTorchDynamo.md** — TORCH_LOGS, explain, config flags, minifiers.
- **TorchInductor/BehaviourOfTorchInductorWithDifferentThingsInstalled.md** — how inductor's codegen choice depends on installed libs (Triton, cudagraphs, oneDNN...).
- **Dynamo internals**: [Trace-Lifecycle](TorchDynamo/Trace-Lifecycle.md), [VariableTracker](TorchDynamo/VariableTracker.md), [Guards](TorchDynamo/Guards.md), [SymPy-Symbolic-Shapes](TorchDynamo/SymPy-Symbolic-Shapes.md)
- **AOTAutograd** — how the compiled region gets its backward graph.
- **FX graph as IR** — the graph Dynamo emits and backends consume.
- **Inductor internals**: [Inductor-IR](TorchInductor/Inductor-IR.md), [Fusion-Scheduler](TorchInductor/Fusion-Scheduler.md)
- **Integration points**: [Integration-Points](Integration-Points.md) (modes, backends, allow_in_graph/disable)

## What to add next

- [x] **Dynamo internals**:
  - [x] The trace lifecycle: frame → bytecode walk → FX graph (link to [bytecode](../../Programming/python/core/bytecode.md))
  - [x] `VariableTracker` — how Python objects are modeled while tracing
  - [x] **Guards** — the shape/type guards Dynamo emits, and recompilation (why `dynamo.config` recompile flags matter)
  - [x] `torch._dynamo.explain` deep dive (extends DebugWithTorchDynamo) → see [Trace-Lifecycle](TorchDynamo/Trace-Lifecycle.md) + [Guards](TorchDynamo/Guards.md)
- [x] **AOTAutograd** — how the compiled region gets its backward graph (the piece between Dynamo and Inductor that I haven't covered).
- [x] **FX graph** as IR — its op set, where Dynamo's output lives (see [IR](../IR/PLAN.md) taxonomy: graph IR vs CFG).
- [x] **Inductor internals**:
  - [x] The loop-level IR (`torch._inductor.ir`): `TritonTemplateBuffer`, `Pointwise`/`Reduction` ops
  - [x] The fusion scheduler (`can_fuse`, `score_fusion`) — when ops fuse and why
  - [x] SymPy symbolic shapes + guards (extends the existing notes)
  - [ ] Reading actual generated Triton/C++ code from a real model (hands-on; see [Inductor-IR](TorchInductor/Inductor-IR.md) for the logging entry points)
- [x] **Integration points**:
  - [x] `torch.compile` modes (`default`, `reduce-overhead`, `max-autotune`) — what each changes
  - [x] `torch.compile` + custom ops / extensions (see [conditioning-on-a-python-subclass](../../Programming/python/core/conditioning-on-a-python-subclass.md))
  - [x] cudagraphs interaction (extends BehaviourOfTorchInductor...)
  - [x] Backend options (`torch.compile(backend=...)`, `torch._dynamo.list_backends`)

## Prioritization

1. **Guards + recompilation** — the #1 real-world performance gotcha.
2. **AOTAutograd** — fills the biggest gap in the compile pipeline story.
3. **Inductor IR + generated code** — hands-on and directly useful.
