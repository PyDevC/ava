# PyTorch/Compilers — Plan

## Goal of this directory

Document the **`torch.compile` stack** — the PyTorch-native compiler pathway (Dynamo → AOTAutograd → Inductor) — with entry points and internals. This is where my PyTorch and compiler interests meet, so it's the directory I want to grow the most. Related notes live in [[../PLAN]] (the PyTorch system view) and [[../Compilers/PyTorchCompiler/PLAN]] (the compiler view).

## What's already here

- **TorchInductor/EntryPoint** — the call path from `torch.compile()` to `compile_fx` in `torch/_inductor`.

## What to add next

- [ ] **The full pipeline, one note per stage**:
  - [ ] **Dynamo** — tracing, guards, graph breaks (see [[../Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]])
  - [ ] **FX graph** — the graph IR in between (op set, nodes, subgraph semantics)
  - [ ] **AOTAutograd** — how backward is compiled too
  - [ ] **Inductor** — loop-level IR, scheduler, Triton/C++ codegen (see [[../Compilers/DLCompilers/TorchInductor/TorchInductor]])
  - [ ] **Runtime** — how the compiled module executes, cudagraphs
- [ ] **`torch.compile` user guide**:
  - [ ] Modes: `default`, `reduce-overhead`, `max-autotune` — what each enables
  - [ ] Backends: `torch.compile(backend=...)`, `torch._dynamo.list_backends`
  - [ ] When compile helps vs hurts (graphs breaks, dynamic shapes)
- [ ] **Inductor hands-on** (I learn best by reading output):
  - [ ] Read the Triton kernel generated for a fused model
  - [ ] Understand fusion decisions (`can_fuse`, `score_fusion`)
  - [ ] Dynamic shapes / symbolic shapes handling
- [ ] **Debugging & optimization workflows**:
  - [ ] `TORCH_LOGS=+inductor,dynamo` deep dives (extends [[../Compilers/PyTorchCompiler/TorchDynamo/DebugWithTorchDynamo]])
  - [ ] Profiling compiled vs eager: where the wins/losses are

## Prioritization

1. **AOTAutograd + Inductor IR** — the two stages my current notes barely cover.
2. **Modes/backends user guide** — the practical "which knob" knowledge.
3. **Reading real generated kernels** — hands-on verification of the whole story.
