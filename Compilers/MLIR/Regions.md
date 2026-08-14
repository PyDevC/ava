# MLIR regions and nesting

**Regions** are what make MLIR "two-level": an operation can *contain* a block of other operations. Nested structures (if bodies, loop bodies, functions, modules) are all expressed the same way — as a region inside an op.

## The mechanics

- A region is an ordered list of **blocks**; each block is an ordered list of **operations** ending in a terminator.
- **Block arguments** (like SSA values, but "entered" from outside) give the nested code its inputs — `scf.for`'s induction variable and iterargs are block arguments, not ops.
- **Terminators**: every block ends in a terminator op (`scf.yield`, `cf.br`). This is how control flow is explicit: values exiting a region are the terminator's operands.

```
scf.for %i = %lb to %ub step %st iter_args(%acc = %init) {
  %v = arith.addi %acc, %i : index      // %i, %acc are block arguments
  scf.yield %v : index                  // terminator hands %v back
}
```

## Why nesting matters

- **Hierarchy without loss**: high-level structure (a loop nest, an affine region) stays explicit and optimizable — no flattening to CFG until *you* decide (see [Passes](Passes.md) lowering order).
- **SSA stays local**: values can't be used outside the region that defines them (with capture rules for ops that allow it) — the two-level structure keeps SSA's single-assignment property clean at every level.
- **The "two-level IR" idea**: one IR that is simultaneously high-level (nesting = structure, see `linalg`/`scf`) and low-level (flattened blocks, see `cf`/`llvm`). Lowering = progressively *un-nesting*.

## Ops with regions

- `func.func`, `module` — top-level nesting.
- `scf.if` / `scf.for` / `scf.while` — control flow as regions ([scf](Dialects/scf-vector.md)).
- `affine.for` — polyhedral loops ([Affine](Dialects/Affine.md)).
- `linalg.generic` — an op whose body defines a per-element computation ([linalg](Dialects/linalg.md)).
- A region is also how **custom ops carry semantics**: any dialect op can declare `let regions = 1` and embed a computation body.

## Related

- [Operations-Types-Values](Operations-Types-Values.md) — ops/values/types; regions are the 4th element of the model.
- [scf](Dialects/scf-vector.md) — structured control flow via regions.
- [Passes](Passes.md) — passes traverse/manipulate this nested structure.
