# Affine

Affine is a Polyhedral compilation dialect to make dependencies

The `affine` dialect models structured control flow with affine (linear) index expressions, which is what enables polyhedral analysis and transformations like loop fusion, tiling, and parallelization. It lives between the general `scf`/`arith` level and the target-specific backends.

## Core ops

- `affine.for` — a loop with a trip count and an induction variable that can be used in affine expressions:
  ```
  affine.for %i = 0 to 8 step 2 {
    ...
  }
  ```
- `affine.parallel` — a parallel loop nest (the loops are independent); lowered to scf.parallel or target parallelism later.
- `affine.if` — condition branch based on an affine condition.
- `affine.load` / `affine.store` — memory access where the indices are affine expressions of enclosing loop IVs and symbols. This is what makes dependence analysis tractable: the access function is a linear map.
- `affine.min` / `affine.max` / `affine.apply` — compute affine expressions.

## Dims and symbols

Every affine map distinguishes:
- **dimensions** — induction variables of enclosing affine.for loops.
- **symbols** — values that are constant within the affine context (block arguments, function args, etc.).

Only dims and symbols can appear in affine expressions; anything else must be materialized with `affine.apply`.

## Why "polyhedral"

Each `affine.for` nest with affine bounds and access functions defines a polyhedron (an integer point set) in iteration space. The dependence between two accesses is a linear constraint problem over these polyhedra, so you can:
- Compute exact loop nest / data dependences (via [[Dominators]]-style analysis, but over iteration domains).
- Reason about legality of fusion, tiling, interchange.
- Lower to GPU (`gpu.launch`, thread maps) or CPU (`scf.for` + `linalg`/vector) backends.

## Lowering

The classic pass is `-convert-affine-to-standard` (a.k.a. `lower-affine`), which turns `affine.for` into `scf.for` and `affine.load/store` into `memref.load/store`. After that, polyhedral information is gone, so all the good analysis must happen *before* lowering.
