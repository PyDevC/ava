# Inductor's loop-level IR

TorchInductor lowers the FX graph (see [FX-Graph-IR](../FX-Graph-IR.md)) into its own **loop-level IR** (`torch._inductor.ir`) — the place where tensor ops become explicit loops over buffers, which then becomes Triton/C++ (see [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md)). This is Inductor's equivalent of MLIR's linalg+scf world (see [linalg](../../MLIR/Dialects/linalg.md)).

## The core op types

- **`Pointwise`** — an elementwise computation: one loop nest over the output shape, each iteration computes one output element (`x + y`, `relu`, `* 2`). The unit of "fuse elementwise ops".
- **`Reduction`** — a reduction (sum, max, mean) with `reduce_ops` + `reduce_ranges`; becomes a *reduction* Triton kernel or a loop in C++.
- **`TemplateBuffer`** (`TritonTemplateBuffer`) — a placeholder for a **pre-written template kernel** (from CUTLASS/Triton templates) that Inductor will *fill in* with tile/split sizes during autotuning — the "use a library kernel" path vs the "generate from IR" path (see [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md) for the templates story).
- Others: `Convolution` (NN), `ExternalKernelCall`, `Scan`, `ExternKernelAlloc`, plus `ReinterpretView`/`View` for aliasing.

## The Scheduler

The **fusion scheduler** is what turns the FX graph (many small ops) into the loop-level IR (few big kernels). It:
1. Builds a DAG of the ops with their memory dependencies.
2. **`can_fuse`** decides if two ops can share a kernel: same output shape/parallel structure, no data-dependent control flow, compatible "memory location" (same buffer), and **`score_fusion`** picks *which* candidates to actually fuse when there are choices (cost heuristic — favor fusing into reductions, avoid fusing around `ExternKernel` boundaries).
3. Emits the fused kernels in dependency order.

## Why "loop-level" is the right spot

- Elementwise fusion needs to see *iterations*, not ops: `relu(x) + y` becomes one loop with `out = relu(x[i]) + y[i]` — only possible below the tensor-op level.
- The `Tile`/`num_warps`/`max_autotune` decisions (see [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md)) are made *here*, at IR construction time, by emitting variants for the autotuner.
- Reading the IR (`TORCH_LOGS=output_code`, or `torch._inductor.config.trace.enabled`) shows exactly which ops got fused — the "why is my graph N kernels" answer.

## Related

- [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md) — the full compile pipeline this IR feeds.
- [Fusion-Scheduler](Fusion-Scheduler.md) — how kernels get chosen.
- [linalg](../../MLIR/Dialects/linalg.md) — the MLIR-world analog.
- [Comparison-Matrix](../../DLCompilers/Comparison-Matrix.md) — where Inductor fits vs other compilers.
