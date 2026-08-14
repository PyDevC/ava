# DRR (Declarative Rewrite Rules)

DRR is MLIR's **declarative pattern language** — you write rewrite rules in TableGen (`.td`), and `mlir-tblgen -gen-rewriters` generates the `RewritePattern` C++ for you. If ODS defines ops, DRR defines *transformations*.

## The syntax

```tablegen
def SimplifyAddZero : Pat<
  (AddOp $lhs, (ConstantOp $zero)),   // pattern: add(x, 0)
  (replaceWithValue $lhs)             // replacement: x
>;
```

- **Pattern side**: a nested op tree using ODS ops (and their bindings `$lhs`).
- **Replacement side**: the resulting op tree (or `replaceWithValue`, `returnType` constraints).
- Guards via `Pat<(AddOp $l, $r), (MulOp ...), [(Constraint1:$l), (Constraint2:$r)]>` — predicates on bindings.
- Native C++ escaped inline with `NativeCodeCall` for non-ODS logic (e.g. `(returnType $lhs)` in the example).

## The two uses

1. **Canonicalization patterns** — a `Folded`/`CanonicalizationPattern` set (see [Canonicalization](Canonicalization.md)): `Pat` rules get the op into normal form.
2. **DialectConversion patterns** — `DialectConversionPattern` via `Let NativeType = "ConvertOpToLLVMPattern"` etc., so DRR rules can drive *legalization* against a target dialect (see [DialectConversion](DialectConversion.md)).

## When to use DRR vs hand-written patterns

- **DRR** for the common case: pure op→op rewrites that fit the declarative mold (folds, simplifications, type lifts). Readable, testable, generated.
- **Hand-written `RewritePattern`** for the rest: conditional logic that needs C++, multi-op reasoning with analysis, conversions that need custom arguments/auxiliary ops, anything requiring `matchAndRewrite` state. You can mix: one `RewritePatternSet` holding both DRR-generated and hand-written patterns.

## The generated artifacts

`mlir-tblgen -gen-rewriters -I ... file.td -o RewritePatterns.inc`, then `#include` it inside a `RewritePatternSet` in a pass. The pass still drives it (via `applyPatternsAndFoldGreedily` or a `ConversionTarget`), DRR just writes the patterns for you.

## Why it matters

- Pattern code is the *bulk* of any real compiler; DRR makes it data, reviewable as a spec and regenerated — less C++ plumbing, fewer bugs.
- It's also how the **Toy tutorial** writes most of its "canonicalize to the right shape" logic — read that to see it in context (see [Toy-Tutorial](Toy-Tutorial.md)).

## Related

- [TableGen](TableGen.md) — the `.td` framework DRR is written in.
- [Canonicalization](Canonicalization.md) — where fold-patterns land.
- [DialectConversion](DialectConversion.md) — where conversion-patterns land.
- [Toy-Tutorial](Toy-Tutorial.md) — worked examples.
