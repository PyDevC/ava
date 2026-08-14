# DialectConversion

DialectConversion is **the pattern-rewriting framework at the heart of MLIR** — the machinery behind every legalization and every dialect-to-dialect lowering (affine→scf, scf→cf, everything→llvm).

## The core idea: legality-driven rewriting

Instead of "apply this one rewrite", you declare **what your target dialect considers legal** and let the framework rewrite *everything else* into legal ops:

- `ConversionTarget` — which ops are legal/illegal/dynamically legal (legal *conditionally*, e.g. only for certain types).
- `RewritePattern`s — each a small `matchAndRewrite(op) -> op or failure`. Patterns for different dialects/ops; the driver applies them.
- **Type conversion** (`TypeConverter`) — a map: `f32 → llvm.f32`, `tensor<...> → memref<...>`, `memref<...> → !llvm.ptr` (signature conversion). When types change, arguments get converted at block boundaries (conversion *rewriters* materialize the conversion in IR).

## The driver loop

1. Walk the IR; for each illegal op, try patterns (bottom-up, with a worklist).
2. **Dynamic legality** decides per-op if it's already acceptable (avoid rewriting what's fine).
3. If the op (or its *operand types*) must change, the framework generates **conversion patterns** that: replace the op, convert its operands' types, and fix up block arguments + results.

The famous `-convert-scf-to-cf`: marks `scf.for`/`scf.if` illegal, provides patterns, marks `cf.br`/`cf.cond_br` + `arith` legal → the framework flattens the structured loops into a CFG, converting types/indices along the way.

## Partial conversion and the "materialization" trick

- Partial conversion: only some ops get legalized; conversion "materializes" *unresolved* type conversions with `builtin.unrealized_conversion_cast` ops. These are resolved by later passes — this is how multi-stage pipelines (tensor→memref→llvm) each leave "bridges" for the next stage.
- **Failure**: if a pattern fails, the driver marks the op to retry or reports failure if it stays illegal — this is why "make every op legal" is the debugging discipline (`--mlir-print-ir-after-failure` is your friend).

## Why it's the heart

Every real MLIR compiler's core is: *declare target legality, write patterns, convert*. TorchInductor, IREE, and XLA all have dialect-conversion machinery — MLIR just makes it a first-class, reusable framework instead of hand-rolled IR rewriting.

## Related

- [[Passes]] — conversion passes are built on this.
- [[Canonicalization]] — the "match+rewrite but same dialect" cousin.
- [[TableGen]] — DRR lets you write conversion patterns declaratively.
- [[../Dialects/scf-vector]] / [[../Dialects/LLVM-Dialect]] — the concrete conversions.
