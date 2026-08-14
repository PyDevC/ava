# Fusion scheduler (can_fuse / score_fusion)

The fusion scheduler is the pass in TorchInductor that decides **which ops get compiled into the same kernel** (see [Inductor-IR](Inductor-IR.md)). It's the difference between "500 small launches" and "5 fused kernels" — and the #1 lever on compiled performance.

## What can fuse (`can_fuse`)

Two ops can share a kernel only if they're **compatible**:
- Same output shape / parallel iteration structure (a Pointwise over `[M,N]` fuses with a Pointwise over `[M,N]`, not with a reduction over a different domain).
- No data-dependent control flow between them (a `masked`/conditional op whose mask depends on values breaks fusion).
- No intermediate that must be materialized as a *separate* buffer (usually due to aliasing/functionalization constraints, see [AOTAutograd](../AOTAutograd.md)).
- Memory location compatible — same device/stream, and the producer's output can live as a fused intermediate (not an `ExternKernel` result, which can't be fused into).

## What *should* fuse (`score_fusion`)

`can_fuse` is a cheap filter; `score_fusion` is the **cost heuristic** deciding among the legal candidates. It's heuristic ("score" by estimated FLOPs/launch-savings), prioritizing:
- Fusing *into* reductions (elementwise + reduction = one kernel instead of two launches — huge win).
- Fusing same-shape elementwise chains (launch overhead + memory traffic savings).
- *Avoiding* fusing around `ExternKernel`s (library calls that must materialize their I/O).

## Why it matters in practice

- **Autotuning + fusion interact**: the scheduler emits *candidate* fused kernels; the autotuner (see [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md)) benchmarks variants (including unfused splits) and picks the winner. `mode="max-autotune"` spends more time here.
- **Debugging**: `TORCH_LOGS=scheduler` / `output_code` shows the schedule and each fusion decision — the standard answer to "why does my graph produce 30 kernels?"
- **Splitting** (`split_into_standard`): the scheduler also decides when NOT to fuse (memory-bound reductions split from compute to let each kernel be optimal).

## The mental model

```
FX graph → deps DAG → [can_fuse: legality] → [score_fusion: cost] → kernels
```

Fusion = legality + cost. It's the loop-level analogue of MLIR's linalg tiling/fusion transforms ([linalg](../../MLIR/Dialects/linalg.md)), but done on Inductor's own IR.

## Related

- [Inductor-IR](Inductor-IR.md) — the ops the scheduler fuses.
- [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md) — the pipeline + autotune interplay.
- [Guards](../TorchDynamo/Guards.md) — guards gate recompiles, but fusion gates kernel count.
- [scf-vector](../../MLIR/Dialects/scf-vector.md) — the same idea in MLIR land.
