# Canonicalization

Canonicalization is MLIR's **always-safe simplification pass**. Every dialect's ops register canonicalization patterns; running `-canonicalize` folds constants, peels trivial cases, and normalizes IR shape — *without changing semantics*.

## What it does

- **Constant folding**: `arith.addi %c1, %c2` → `%c3` (needs the op's folder + a constant materializer).
- **Trivial rewrites**: `x + 0 → x`, `x * 1 → x`, `tensor.extract_slice` of a full tensor → the tensor, `select %true, a, b → a`.
- **Deduplication / normalization**: identical ops → same value (this is how CSE *finds* duplicates, see [[../../IR/CommonSubexpressionElimination]]), operands in canonical order (`add` is commutative → operands sorted).

## The contract

- Patterns are **beneficial for all inputs** and **reduce the IR** monotonically-ish (a cost model may gate some). A pattern that's only sometimes good goes in a *pass*, not a canonicalizer.
- Canonicalization is applied **between passes** as a cleanup: passes assume their input is already canonical (no trivial constants, operands normalized), which is how the whole pipeline stays predictable.

## Why every op needs one

- A dialect's "canonicalizer" is what makes the dialect *usable* — without it, downstream passes see `x+0`, `x*1`, dead branches, and the legality checks in [[../DialectConversion]] produce messy output.
- It's cheap and runs everywhere: `mlir-opt -canonicalize`, inside `mlir::applyPatternsAndFoldGreedily`, and automatically as part of `--mlir-opt` pipelines.
- Writing a canonicalizer for your op is the *first* thing you do in [[TableGen]] ODS (`let hasCanonicalizer = 1` → `populateFolders` / a `RewritePattern`).

## Related

- [[TableGen]] — `hasCanonicalizer`, DRR patterns.
- [[DialectConversion]] — the pattern framework it reuses.
- [[../../IR/CommonSubexpressionElimination]] — the CSE pass canonicalization feeds.
- [[../../IR/DeadCodeElimination]] — DCE cleans up what canonicalization exposes.
