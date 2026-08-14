# MLIR/Dialects — Plan

## Goal

Notes on **individual MLIR dialects** — the vocabulary MLIR is made of.

## What's here

- [[Affine]] — the polyhedral dialect: affine.for/parallel, dims/symbols, lowering.

## What to add next (see also [[../PLAN]])

- [ ] `scf` (structured control flow) — the general loop/if dialect
- [ ] `arith` / `vector` — arithmetic and vectorization
- [ ] `linalg` — ops-on-tensors, the DL lowering target
- [ ] `memref` / `tensor` — the two value models
- [ ] `tosa` / `stablehlo` — the ML frontend dialects IREE consumes
- [ ] LLVM dialect (`llvm.*`) — the bottom of the ladder
- [ ] The Toy tutorial's dialect as a worked example
