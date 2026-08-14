# PyTorchCompiler/TorchInductor — Plan

## Goal

Notes on **TorchInductor's behavior** as seen from the compiler side of `torch.compile`.

## What's here

- [BehaviourOfTorchInductorWithDifferentThingsInstalled](BehaviourOfTorchInductorWithDifferentThingsInstalled.md) — how installed libraries change codegen (Triton, cudagraphs, oneDNN, CUTLASS).
- [EntryPoint](EntryPoint.md) — the `torch.compile()` → `compile_fx` call chain.
- [Inductor-IR](Inductor-IR.md) — the loop-level IR in detail (Pointwise/Reduction ops, templates).
- [Fusion-Scheduler](Fusion-Scheduler.md) — `can_fuse`, `score_fusion`, why ops fuse or not.

## What to add next (see also [PLAN](../PLAN.md) and [TorchInductor](../../DLCompilers/TorchInductor/PLAN.md))

- [x] The loop-level IR in detail (Pointwise/Reduction ops)
- [x] Fusion scheduler (`can_fuse`, `score_fusion`) — why ops fuse or not
- [ ] Reading generated Triton/C++ for a real model (hands-on, via `TORCH_LOGS=output_code`)
- [x] `torch.compile` modes (`default`/`reduce-overhead`/`max-autotune`) — what changes (see [Integration-Points](../Integration-Points.md))
- [x] Backend hooks (`torch._dynamo.list_backends`, custom backends) (see [Integration-Points](../Integration-Points.md))
