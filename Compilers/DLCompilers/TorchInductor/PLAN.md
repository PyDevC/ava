# TorchInductor — Plan

## Goal

Note on **TorchInductor** (PyTorch's default compile backend): the define-by-run loop IR, symbolic shapes + guards, fusion scheduler, and Triton/C++ codegen.

## What's here

- [[TorchInductor]] — overview of the inductor internals (see the two sibling notes in [[../Compilers/PyTorchCompiler/TorchInductor]] and [[../../PyTorch/Compilers/TorchInductor]] too).

## What to add next (see also [[../PLAN]] and [[../PyTorchCompiler/PLAN]])

- [ ] Read actual generated Triton kernels from a fused model
- [ ] The scheduler's fusion decisions (`can_fuse`, `score_fusion`) in detail
- [ ] Symbolic shapes / guards lifecycle (Dynamo → Inductor)
- [ ] Templates (conv/matmul: cuDNN/oneDNN/CUTLASS) — when inductor picks them over Triton
- [ ] Inductor vs IREE on the same model — a concrete comparison note
