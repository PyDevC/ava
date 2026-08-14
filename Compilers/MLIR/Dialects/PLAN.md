# MLIR/Dialects — Plan

## Goal

Notes on **individual MLIR dialects** — the vocabulary MLIR is made of.

## What's here

- [[Affine]] — the polyhedral dialect: affine.for/parallel, dims/symbols, lowering.
- [[scf-vector]] — structured control flow + vectorization dialects.
- [[linalg]] — ops-on-tensors, the DL lowering target.
- [[Tensor-memref]] — the two value models.
- [[tosa-stablehlo]] — the ML frontend dialects IREE consumes.
- [[LLVM-Dialect]] — the bottom of the ladder.

## What to add next (see also [[../PLAN]])

- [x] `scf` (structured control flow) — the general loop/if dialect
- [x] `arith` / `vector` — arithmetic and vectorization
- [x] `linalg` — ops-on-tensors, the DL lowering target
- [x] `memref` / `tensor` — the two value models
- [x] `tosa` / `stablehlo` — the ML frontend dialects IREE consumes
- [x] LLVM dialect (`llvm.*`) — the bottom of the ladder
- [ ] The Toy tutorial's dialect as a worked example → see [[../Toy-Tutorial]] and the Ch7 "add an op" walkthrough
