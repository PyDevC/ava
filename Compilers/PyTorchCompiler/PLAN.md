# PyTorchCompiler — Plan

## Goal of this directory

Understand PyTorch's **compiler stack** (`torch.compile`): how TorchDynamo traces Python into a graph, how AOTAutograd handles autograd, and how TorchInductor lowers that graph to fast Triton/C++ code. This is the most relevant compiler work to me (I use PyTorch daily), and it ties together [[../PLAN]]'s DL-compiler theory with PyTorch's internals (see [[../../PyTorch/PLAN]]).

## What's already here

- **TorchDynamo/GraphBreaks.md** — what causes graph breaks, how to find/reduce them.
- **TorchDynamo/DebugWithTorchDynamo.md** — TORCH_LOGS, explain, config flags, minifiers.
- **TorchInductor/BehaviourOfTorchInductorWithDifferentThingsInstalled.md** — how inductor's codegen choice depends on installed libs (Triton, cudagraphs, oneDNN...).

## What to add next

- [ ] **Dynamo internals**:
  - [ ] The trace lifecycle: frame → bytecode walk → FX graph (link to [[../../Programming/python/core/bytecode]])
  - [ ] `VariableTracker` — how Python objects are modeled while tracing
  - [ ] **Guards** — the shape/type guards Dynamo emits, and recompilation (why `dynamo.config` recompile flags matter)
  - [ ] `torch._dynamo.explain` deep dive (extends DebugWithTorchDynamo)
- [ ] **AOTAutograd** — how the compiled region gets its backward graph (the piece between Dynamo and Inductor that I haven't covered).
- [ ] **FX graph** as IR — its op set, where Dynamo's output lives (see [[../../Compilers/IR]] taxonomy: graph IR vs CFG).
- [ ] **Inductor internals**:
  - [ ] The loop-level IR (`torch._inductor.ir`): `TritonTemplateBuffer`, `Pointwise`/`Reduction` ops
  - [ ] The fusion scheduler (`can_fuse`, `score_fusion`) — when ops fuse and why
  - [ ] SymPy symbolic shapes + guards (extends the existing notes)
  - [ ] Reading actual generated Triton/C++ code from a real model
- [ ] **Integration points**:
  - [ ] `torch.compile` modes (`default`, `reduce-overhead`, `max-autotune`) — what each changes
  - [ ] `torch.compile` + custom ops / extensions (see [[../../Programming/python/core/conditioning-on-a-python-subclass]])
  - [ ] cudagraphs interaction (extends BehaviourOfTorchInductor...)
  - [ ] Backend options (`torch.compile(backend=...)`, `torch._dynamo.list_backends`)

## Prioritization

1. **Guards + recompilation** — the #1 real-world performance gotcha.
2. **AOTAutograd** — fills the biggest gap in the compile pipeline story.
3. **Inductor IR + generated code** — hands-on and directly useful.
