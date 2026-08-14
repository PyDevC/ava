# DISC's two-level design (graph + kernel)

DISC compiles at two granularities on purpose: a **graph-level IR** for cross-op optimization and a **kernel-level IR** for per-kernel codegen. The split is what lets it optimize the whole model *and* generate good code for each fused group (see [DISC](DISC.md) for the overview).

## Level 1: graph-level IR

- Based on **XLA HLO** (BladeDISC extends the MHLO/CHLO dialect) — the same graph IR XLA uses, so it inherits the mature HLO optimization pass set.
- What happens here: **cross-op optimizations** — constant folding, algebraic simplification, layout/broadcast cleanups, and the big one: **deciding which ops fuse into one kernel**. That decision is the graph partitioner.
- Dynamic shapes are first-class at this level (dims are symbolic expressions, see [Dynamic shapes](DISC-Dynamic-Shapes.md)).

## Level 2: kernel-level IR

- MLIR-based, **linalg-like** (and Triton-like for some backend codegen): one IR per kernel, loops + tensor ops, ready for tiling/vectorization and codegen.
- What happens here: per-kernel optimization — tiling, loop order, vectorization, shared-memory placement, and backend-specific lowering to CUDA/ROCm/CPU (LLVM).
- Each kernel is compiled independently — so a bad partitioner choice (e.g. a kernel too big for shared memory) shows up as a slow kernel you can't fix from this level.

## How the two levels interact

```
graph IR (HLO, whole model)
   |  graph partitioner: which ops fuse into one kernel?
   v
fused groups → kernel IR (linalg-like) → tile/vectorize → backend codegen
   |  (shape info from the graph level flows in as symbolic bounds)
   v
CUDA/HIP/CPU binary
```

The **graph partitioner is the hinge**: it's DISC's analogue of Inductor's fusion scheduler (see [Fusion-Scheduler](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md)), but it runs on the HLO graph with symbolic shapes, and it must respect dynamic-shape constraints — fusing two ops is only legal if their symbolic shapes can be proven compatible. The kernel level then only sees a fixed, well-typed fused group.

## Why it matters

The two-level split is why DISC can be both a *model* optimizer and a *kernel* compiler without one getting in the way of the other — and why it beats an approach that only does one (pure graph optimization leaves kernels slow; pure kernel codegen misses cross-op fusion).

## Related

- [DISC](DISC.md) — overview and motivation.
- [Dynamic shapes](DISC-Dynamic-Shapes.md) — the shape machinery that constrains the partitioner.
- [Serving](DISC-Serving.md) — where this shows up in Alibaba's stack.
- [Fusion-Scheduler](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md) — the same hinge decision in Inductor.
