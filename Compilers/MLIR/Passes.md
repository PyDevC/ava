# MLIR passes and pass pipelines

MLIR does transformations as **passes** — each pass walks the IR, rewrites ops, and reports what changed. A **pipeline** is an ordered list of passes. This is the "compile" half of the framework.

## Pass manager basics

- `mlir::PassManager` runs passes in order over a `ModuleOp`. Each pass runs per **operation of a chosen level** (module / function / etc.) — `PassManager` with `nest`/op-specific passes controls granularity.
- A pass declares `getArgument()` (the `-flag` name), `getDependentDialects()`, and an `Option` struct for CLI knobs.
- Running `mlir-opt --pass-name file.mlir` shows the effect on IR; `--pass-pipeline='func.func(my-pass),arith-addi-to-f32'` chains them.

## Types of passes

- **Transforms**: rewrite IR in place — `-canonicalize`, `-cse`, `-symbol-dce`.
- **Conversions / legalizations**: move IR between dialects — `-convert-affine-to-scf`, `-convert-scf-to-cf`, `-convert-to-llvm`, `-one-shot-bufferize`. These are the ones built on **DialectConversion** (see [DialectConversion](DialectConversion.md)).
- **Analyses**: compute facts (e.g. number of loops) — done inside passes, not as standalone passes.
- **Canonicalization** (see [Canonicalization](Canonicalization.md)): a special always-run cleanup.

## The standard pipeline shape

```
lower to linalg → bufferize (tensor→memref) → scf → vector → convert to llvm
```

The canonical DL lowering ladder. `-one-shot-bufferize` is the modern bufferization entry; the "default" lowering pipeline documents the sequence on the MLIR site (see [tosa-stablehlo](Dialects/tosa-stablehlo.md) for the frontend side).

## Writing a pass

```cpp
struct MyPass : public PassWrapper<MyPass, OperationPass<func::FuncOp>> {
  void runOnOperation() final {          // operates on each func.func
    // getOperation(), walk ops, modify, or markAndPropagateAnalysisFailure
  }
  StringRef getArgument() const final { return "my-pass"; }
};
```

Register via `registerPass` + `PassRegistration` so `mlir-opt --my-pass` works. In `add_mlir_library` targets the pass must be in an `mlir-opt` plugin or linked into a tool (see [CMake_Guide](CMake_Guide.md)).

## Gotchas I keep hitting

- Passes run on a **snapshot**; you must call `runOnOperation` and return a new `Operation` if you replace the op (or use the pattern driver).
- **Order matters** for legality: converting before legalizing means the later pass sees the *wrong* dialect. This is why "legalization" pipelines are carefully ordered.
- Caching: `mlir-opt` print stats with `--mlir-pass-statistics`; passes that don't declare dependencies can be reordered — declare them (`getDependentDialects`).

## Related

- [DialectConversion](DialectConversion.md) — the framework most "real" passes use.
- [Canonicalization](Canonicalization.md) — the always-on cleanup pass.
- [CMake_Guide](CMake_Guide.md) — wiring a pass into a build.
- [scf-vector](Dialects/scf-vector.md) — the concrete pipelines these passes form.
