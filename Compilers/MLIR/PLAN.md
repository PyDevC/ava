# MLIR — Plan

## Goal of this directory

Learn **MLIR** well enough to read and write dialects, passes, and lowerings — since every DL compiler I care about (IREE, TorchInductor's guts, XLA-to-HLO, TVM's modern path) is built on or inspired by it. The practical goal: build a small dialect + conversion to prove I understand the framework.

## What's already here

- **CMake_Guide.md** — how to structure a CMake project using MLIR (`find_package`, `mlir_tablegen`, `add_mlir_library`).
- **Core concept notes**: [[Operations-Types-Values]], [[Regions]], [[Builtin-Dialects]] (`arith`, `func`, `memref`)
- **Dialects**: [[Dialects/Affine]], [[Dialects/scf-vector]], [[Dialects/linalg]], [[Dialects/Tensor-memref]], [[Dialects/tosa-stablehlo]], [[Dialects/LLVM-Dialect]]
- **Passes & rewrites**: [[Passes]], [[DialectConversion]], [[Canonicalization]]
- **TableGen / ODS**: [[TableGen]], [[DRR]]
- **Hands-on**: [[Toy-Tutorial]] (the map), a tiny dialect (see [[Toy-Tutorial]] template)

## What to add next

- [x] **Core concept notes**:
  - [x] What an operation/type/attribute is; the `Operation`/`Value` model, SSA in MLIR (see [[../IR/SSA/Dominators]])
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
