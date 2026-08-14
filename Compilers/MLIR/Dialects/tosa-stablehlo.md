# tosa and stablehlo dialects

`tosa` and `stablehlo` are the **frontend dialects** — the two standardized entry points that DL frameworks lower *into* before anything MLIR-specific happens. A hardware compiler (IREE, a TVM-style backend) consumes one of these and never cares about PyTorch/JAX/TensorFlow again.

## StableHLO — the Google/JAX story

- Comes from XLA's HLO (see [StableHLO](../../DLCompilers/XLA-StableHLO.md)). `stablehlo.add`, `stablehlo.dot_general`, `stablehlo.convolution` — a portable, versioned form of HLO.
- The interchange format: **PyTorch, JAX, TF** all have `to stablehlo` exporters. The goal: stable semantics across compiler versions and vendors (MLIR "portable" spec).
- Op set is the *high-level tensor ops* — einsum-style `dot_general` with dimension contracts, `convolution`, `reduce_window`, `scatter`, `gather` — no loops, no scalar code visible.

## TOSA — Tensor Operator Set Architecture

- The ARM/ML embedded-target analogue: a **stable operator set** for ML, targetable by any framework. Same niche as StableHLO but oriented to embedded/edge (TOSA ops lower toward CMSIS/ARM SIMD well).
- More "linear" op set than StableHLO (fewer exotic ops; shapes/quantization handled per-op), which makes it easier for *edge* codegen.

## The role in the pipeline

```
PyTorch/JAX/TF
    │  (exporters)
    ▼
tosa / stablehlo ──(legalize)──▶ linalg
                                   │ (transform: tile/fuse/vectorize)
                                   ▼
                               vector/scf/memref → llvm/gpu
```

Both dialects lower to **linalg** ([linalg](linalg.md)) — the generic structured target where the real optimization happens. The frontend dialect choice is about *portability and standardization*, not about optimization; the linalg step is where fusion/vectorization lives.

## Why learn them

- If you write an MLIR-based compiler, you *consume* one of these (not raw PyTorch graphs). Knowing the op sets means knowing your input contract.
- The standardization story (StableHLO as interchange format, `torch_mlir` → linalg path) is how the whole ML compiler ecosystem stays composable.

## Related

- [linalg](linalg.md) — the lowering target for both.
- [StableHLO](../../DLCompilers/XLA-StableHLO.md) — StableHLO's compiler-side parent.
- [TVM](../../DLCompilers/TVM.md) — TVM's equivalent frontend story.
- [ONNXRuntime](../../DLCompilers/ONNXRuntime.md) — the other "standard op set" world (ONNX).
