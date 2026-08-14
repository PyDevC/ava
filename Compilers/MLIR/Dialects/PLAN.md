# MLIR/Dialects — Plan

## Goal

Notes on **individual MLIR dialects** — the vocabulary MLIR is made of.

## What's here

- [Affine](Affine.md) — the polyhedral dialect: affine.for/parallel, dims/symbols, lowering.
- [scf-vector](scf-vector.md) — structured control flow + vectorization dialects.
- [linalg](linalg.md) — ops-on-tensors, the DL lowering target.
- [Tensor-memref](Tensor-memref.md) — the two value models.
- [tosa-stablehlo](tosa-stablehlo.md) — the ML frontend dialects IREE consumes.
- [LLVM-Dialect](LLVM-Dialect.md) — the bottom of the ladder.

## What to add next (see also [PLAN](../PLAN.md))

- [x] `scf` (structured control flow) — the general loop/if dialect
- [x] `arith` / `vector` — arithmetic and vectorization
- [x] `linalg` — ops-on-tensors, the DL lowering target
- [x] `memref` / `tensor` — the two value models
- [x] `tosa` / `stablehlo` — the ML frontend dialects IREE consumes
- [x] LLVM dialect (`llvm.*`) — the bottom of the ladder
- [ ] The Toy tutorial's dialect as a worked example → see [Toy-Tutorial](../Toy-Tutorial.md) and the Ch7 "add an op" walkthrough
