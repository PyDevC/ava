# linalg dialect

`linalg` is the **ops-on-tensors** dialect — the sweet spot where DL compilers do their best work. One `linalg.generic` (or `linalg.matmul`) op names an *entire* structured computation: loops, iteration order, and the per-element body, all in one op.

## The idea

Instead of "loop op containing scalar ops" (scf), linalg inverts it: the **op is the loop structure**, and its region body is the pointwise computation.

```
%r = linalg.generic {
       indexing_maps = [affine_map<(d0,d1) -> (d0,d1)>, affine_map<(d0,d1) -> (d0,d1)>],
       iterator_types = ["parallel", "parallel"]
     } ins(%a, %b : tensor<2x3xf32>, tensor<2x3xf32>)
     outs(%c : tensor<2x3xf32>) {
     ^bb0(%va: f32, %vb: f32, %vc: f32):
       %s = arith.addf %va, %vb : f32
       linalg.yield %s : f32
     } -> tensor<2x3xf32>
```

- **indexing_maps** (affine maps, see [[../Dialects/Affine]]) describe *which elements each operand touches* — broadcasting, transposes, and reductions are all just different maps.
- **iterator_types** say which dims are parallel vs reduction.
- The **body** is the scalar computation.

## Why it's the DL lowering target

- **One op = full knowledge**: the compiler knows the exact iteration structure and access pattern → it can do **tiling, fusion, vectorization** (via `linalg`'s transform dialect), which is the actual high-performance engine inside IREE and TorchInductor's linalg paths.
- **Named ops** (`linalg.matmul`, `linalg.conv_2d_nchw_fchw`) are macros over `generic` with fixed maps — the recognizer matches *patterns* (e.g. 4 nested scf loops doing a matmul → `linalg.matmul`), then transforms them.
- Everything high-level lowers *into* linalg (from `tosa`/`stablehlo` frontends), and everything below lowers *out* of it (linalg → scf → vector → llvm, see [[scf-vector]]).

## The flow in a real DL compiler

```
tosa / stablehlo ──▶ linalg (structured)
linalg ──transform: tile/fuse/vectorize──▶ vector + scf
vector ──▶ memref / llvm / gpu
```

Bufferization happens at the linalg→memref boundary: `linalg.generic` on `tensor` values (immutable) becomes `linalg` on `memref` (mutable memory, see [[Tensor-memref]]).

## Related

- [[Tensor-memref]] — the bufferization boundary linalg crosses.
- [[scf-vector]] — what linalg lowers into.
- [[../../../PyTorchCompiler/TorchInductor/TorchInductor]] — Inductor's linalg-style fusion (same ideas, own IR).
- [[../../IR/SSA/Dominators]] — the SSA machinery under the tensors.
