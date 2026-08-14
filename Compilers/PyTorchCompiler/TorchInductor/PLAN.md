# PyTorchCompiler/TorchInductor — Plan

## Goal

Notes on **TorchInductor's behavior** as seen from the compiler side of `torch.compile`.

## What's here

- [[BehaviourOfTorchInductorWithDifferentThingsInstalled]] — how installed libraries change codegen (Triton, cudagraphs, oneDNN, CUTLASS).

## What to add next (see also [[../PLAN]] and [[../../DLCompilers/TorchInductor]])

- [ ] The loop-level IR in detail (Pointwise/Reduction ops)
- [ ] Fusion scheduler (`can_fuse`, `score_fusion`) — why ops fuse or not
- [ ] Reading generated Triton/C++ for a real model
- [ ] `torch.compile` modes (`default`/`reduce-overhead`/`max-autotune`) — what changes
- [ ] Backend hooks (`torch._dynamo.list_backends`, custom backends)
