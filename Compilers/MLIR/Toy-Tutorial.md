# The Toy tutorial

The **Toy** tutorial (in the MLIR docs, `mlir/docs/Tutorials/Toy/`) is the canonical "write a small language with MLIR" walkthrough: a toy tensor-language frontend lowered all the way to LLVM. It's the fastest way to see every MLIR concept in one flowing example.

## What Toy is

A tiny language: tensor values (`Tensor<2x3> t = [[1,2,3],[4,5,6]]`), elementwise ops, matmul, transpose, `def main() {}` functions, generic functions + `generic_call`, plus a `reshape` that produces shape errors at compile time (for the shape-inference chapters). No real semantics — a vehicle for MLIR.

## The chapter map (what each teaches)

1. **Ch1 — Ops & parse**: the AST + the Toy dialect's first op (`toy.constant`), ODS/TableGen definition, printing/parsing (`assemblyFormat`).
2. **Ch2 — Shape inference**: an **analysis** pass (walk the IR, infer tensor shapes, verify errors) — the "pass that computes facts" template (see [[Passes]]).
3. **Ch3 — High-level transformations**: `-toy-combine` — **canonicalization + DRR** rewrites (`reshape(reshape(x)) → reshape(x)`, `transpose(transpose(x)) → x`) (see [[Canonicalization]], [[DRR]]).
4. **Ch4 — Lower to affine**: `toy.* → affine/arith/memref` via **DialectConversion** (see [[DialectConversion]]). This is the big one — writes a ConversionTarget + patterns that turn Toy's tensor ops into affine loops + loads/stores.
5. **Ch5 — Lower to LLVM**: memref→LLVM (`-convert-to-llvm` style), then the module can be **JIT'd** (LLVM's `ExecutionEngine`) and `main()` called from C++.
6. **Ch6 — Optimize in the IR**: loop unrolling (`-affine-unroll`), dataflow for copy elision.
7. **Ch7 — Add a new op**: add `toy.transpose` "the hard way" (full pipeline: ODS, verifier, parser, DRR patterns, lowering).

## The takeaway arc

```
toy.constant (high-level value world)
   │ Ch3: canonicalize + DRR
   ▼
toy ops → [Ch4: DialectConversion] → affine/arith/memref
   │ [Ch5: convert-to-llvm]
   ▼
LLVM IR → JIT → real execution
```

In ~1000 lines you build a whole compiler stack and touch *every* concept in this directory: [[Operations-Types-Values]], [[Regions]], [[TableGen]], [[Canonicalization]], [[DRR]], [[DialectConversion]], [[Passes]], and both [[Dialects/scf-vector]] and [[Dialects/LLVM-Dialect]] as targets.

## Doing it for real

- Grab the code from the MLIR source tree (`mlir/examples/toy/`), build with the [[CMake_Guide]] pattern, and `mlir-opt` each stage. Chapters 1–5 are the must-do; 6–7 are depth.
- The natural next step: my own tiny dialect (the plan's goal) — Toy IS the template for that.

## Related

- Every note in this directory — this is the integration test of all of them.
- [[../../IR/SSA/Dominators]] / [[../../IR/CommonSubexpressionElimination]] — the classic passes you'll reach for once Toy works.
