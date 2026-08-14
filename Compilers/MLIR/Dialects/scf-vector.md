# scf and vector dialects

`scf` (structured control flow) and `vector` are the "structured" siblings of [[../Dialects/Affine]] — high-level dialects with clean semantics that can be aggressively optimized before being lowered to flat control flow.

## `scf` — structured control flow

Control flow **as regions with explicit yield**, not gotos:

- `scf.for %i = %lb to %ub step %st iter_args(...)` — the canonical loop. **iter_args** give a functional-style loop-carried value (the SSA-clean way to express reduction/accumulation — see the example in [[Regions]]).
- `scf.if %c { ... } else { ... }` — structured branch.
- `scf.while` — loop with separate cond/body regions (for `break`-style logic).

Why structured matters: the optimizer sees the *whole* loop (bounds, step, carried values) as one op, so it can do loop unrolling, interchange, fusion, tiling **before** control flow is flattened to CFG. Once lowered to `cf.br`/`cf.cond_br` ([[../Passes]] `-convert-scf-to-cf`), that structure is gone forever.

## `vector` — SIMD-as-structure

`vector<8xf32>` is a *value type*; ops like `vector.add`, `vector.extract`, `vector.transfer_read` express SIMD computation without naming any hardware:

- One dialect spans SSE/AVX/NEON: `vector.transfer_read %mem[...]` loads a vector; `vector.mask`/`vector.scatter` cover predication; `vector.contract` is the matmul kernel primitive.
- `vector.transfer_read/write` are the **memref→vector** bridge (the lowering of `linalg` copies, see [[linalg]]).
- Keeps vector ops high-level and *legal to reorder* (masking, alignment info intact) until the very end, when `-convert-vector-to-llvm` targets the actual ISA.

## The lowering ladder (simplified)

```
affine.for ──convert-affine-to-scf──▶ scf.for
scf.for   ──convert-scf-to-cf──────▶ cf (cfg form)
cf        ──convert-cf-to-llvm─────▶ llvm.br/cond_br
vector    ──convert-vector-to-llvm─▶ llvm (SIMD intrinsics / x86vector)
```

Each step destroys structure; that's why the high-level dialects run first (tiling, fusion, vectorization), then flatten.

## Related

- [[../Dialects/Affine]] — affine is the polyhedral sibling; scf is its general (non-analyzable) form.
- [[../Dialects/linalg]] — linalg ops lower to scf loops.
- [[../Passes]] — the pass flags that perform each step above.
