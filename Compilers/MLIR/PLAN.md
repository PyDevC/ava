# MLIR — Plan

## Goal of this directory

Learn **MLIR** well enough to read and write dialects, passes, and lowerings — since every DL compiler I care about (IREE, TorchInductor's guts, XLA-to-HLO, TVM's modern path) is built on or inspired by it. The practical goal: build a small dialect + conversion to prove I understand the framework.

## What's already here

- **CMake_Guide.md** — how to structure a CMake project using MLIR (`find_package`, `mlir_tablegen`, `add_mlir_library`).
- **Dialects/Affine.md** — the polyhedral `affine` dialect (loops, dims/symbols, lowering).

## What to add next

- [ ] **Core concept notes**:
  - [ ] What an operation/type/attribute is; the `Operation`/`Value` model, SSA in MLIR (see [[../IR/SSA/Dominators]])
  - [ ] Regions (nested), the "two-level" IR idea
  - [ ] The builtin dialects: `arith`, `func`, `memref`
  - [ ] `scf` (structured control flow) and `vector` — Affine's siblings, then
  - [ ] `linalg` — the ops-on-tensors dialect used as a lowering target
- [ ] **Passes**:
  - [ ] Pass manager, pipeline structure, how to write a pass
  - [ ] `-convert-affine-to-scf`, `-convert-scf-to-cf`, standard lowering pipeline
  - [ ] DialectConversion: the pattern rewriting framework (the heart of MLIR)
  - [ ] Canonicalization and why every op needs a canonicalizer
- [ ] **TableGen / ODS**:
  - [ ] Defining an op in `.td` (this extends the CMake note — `mlir_tablegen` generates from it)
  - [ ] `mlir-tblgen` outputs: `.h.inc`, `.cpp.inc`, `-gen-op-decls/-defs`
  - [ ] DRR (`Declarative Rewrite Rules`) for simple rewrites
- [ ] **Hands-on**:
  - [ ] The **Toy tutorial** (mlir-wwwdocs) end-to-end — the canonical way to learn
  - [ ] A tiny dialect of my own: parse → canon → lower → LLVM dialect
  - [ ] Use `mlir-opt` to inspect pass effects on examples

## Prioritization

1. **Toy tutorial** first — it teaches ops, regions, passes, and lowering in one flow.
2. Then **DialectConversion** (the piece that makes real compilers work).
3. Then **ODS/TableGen** depth, which the CMake note only touches.
