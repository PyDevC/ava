# MLIR — Plan

## Goal of this directory

Learn **MLIR** well enough to read and write dialects, passes, and lowerings — since every DL compiler I care about (IREE, TorchInductor's guts, XLA-to-HLO, TVM's modern path) is built on or inspired by it. The practical goal: build a small dialect + conversion to prove I understand the framework.

## What's already here

- **CMake_Guide.md** — how to structure a CMake project using MLIR (`find_package`, `mlir_tablegen`, `add_mlir_library`).
- **Core concept notes**: [Operations-Types-Values](Operations-Types-Values.md), [Regions](Regions.md), [Builtin-Dialects](Builtin-Dialects.md) (`arith`, `func`, `memref`)
- **Dialects**: [Affine](Dialects/Affine.md), [scf-vector](Dialects/scf-vector.md), [linalg](Dialects/linalg.md), [Tensor-memref](Dialects/Tensor-memref.md), [tosa-stablehlo](Dialects/tosa-stablehlo.md), [LLVM-Dialect](Dialects/LLVM-Dialect.md)
- **Passes & rewrites**: [Passes](Passes.md), [DialectConversion](DialectConversion.md), [Canonicalization](Canonicalization.md)
- **TableGen / ODS**: [TableGen](TableGen.md), [DRR](DRR.md)
- **Hands-on**: [Toy-Tutorial](Toy-Tutorial.md) (the map), a tiny dialect (see [Toy-Tutorial](Toy-Tutorial.md) template)

## What to add next

- [x] **Core concept notes**:
  - [x] What an operation/type/attribute is; the `Operation`/`Value` model, SSA in MLIR (see [Dominators](../IR/SSA/Dominators.md))
  - [x] Regions (nested), the "two-level" IR idea
  - [x] The builtin dialects: `arith`, `func`, `memref`
  - [x] `scf` (structured control flow) and `vector` — Affine's siblings, then
  - [x] `linalg` — the ops-on-tensors dialect used as a lowering target
- [x] **Passes**:
  - [x] Pass manager, pipeline structure, how to write a pass
  - [x] `-convert-affine-to-scf`, `-convert-scf-to-cf`, standard lowering pipeline
  - [x] DialectConversion: the pattern rewriting framework (the heart of MLIR)
  - [x] Canonicalization and why every op needs a canonicalizer
- [x] **TableGen / ODS**:
  - [x] Defining an op in `.td` (this extends the CMake note — `mlir_tablegen` generates from it)
  - [x] `mlir-tblgen` outputs: `.h.inc`, `.cpp.inc`, `-gen-op-decls/-defs`
  - [x] DRR (`Declarative Rewrite Rules`) for simple rewrites
- [x] **Hands-on**:
  - [x] The **Toy tutorial** (mlir-wwwdocs) end-to-end — the canonical way to learn
  - [ ] A tiny dialect of my own: parse → canon → lower → LLVM dialect (to-do: build it, then fold notes in)
  - [ ] Use `mlir-opt` to inspect pass effects on examples (follow the Toy tutorial setup)

## Prioritization

1. **Toy tutorial** first — it teaches ops, regions, passes, and lowering in one flow.
2. Then **DialectConversion** (the piece that makes real compilers work).
3. Then **ODS/TableGen** depth, which the CMake note only touches.
