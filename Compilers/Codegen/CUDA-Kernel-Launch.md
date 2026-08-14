# CUDA kernel launch mechanics

The mental model for why GPU code is structured the way it is: the **grid/block/thread hierarchy**, the **occupancy** trade-off, and the cost of a **kernel launch** — which is why fusing kernels is nearly always the right move.

## The hierarchy

```
grid            (all programs, hardware-independent: gridDim.x)
 └─ block       (runs on one SM; blockDim.x threads; shared memory is per-block)
     └─ warp    (32 threads, SIMT: all execute the same instruction in lockstep)
     └─ thread  (has its own registers, program counter, local memory)
```

- Threads in a warp execute together — divergence (an `if` where some threads take the other branch) costs *both* branches for all 32 threads.
- Blocks are scheduled onto SMs independently and never migrate mid-execution. All blocks must finish before the kernel returns.
- Shared memory is visible to a block; global memory to everything.

## Occupancy

Occupancy = the fraction of a block's threads the SM can actually run concurrently. The limiter is whichever resource runs out first:

- **Registers** — each thread needs a count; more registers/thread → fewer resident threads.
- **Threads per block / blocks per SM** — hardware caps.
- **Shared memory** — per-block smem usage divides the SM's smem pool.

Higher occupancy hides **memory latency**: while one warp waits on a global load, the SM runs another warp. But there's a genuine trade-off — using *more* registers per thread (e.g. keeping a tile accumulator alive) can raise a single warp's throughput at the cost of occupancy. This is the register-allocation drama of [RegisterAllocation](../IR/RegisterAllocation.md) playing out on real hardware.

## Why fusion matters

Every kernel launch costs ~**5–10 µs** of overhead (CPU-side command setup + GPU-side queue dispatch). A model that runs 1000 small kernels spends *milliseconds* just launching. Fusion (see [Fusion-Scheduler](../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md)) attacks this two ways:

1. **Fewer launches** — N elementwise ops become 1 kernel.
2. **Data stays in registers** — intermediates never touch global memory; the fused kernel keeps `x + y` in a register instead of writing `tmp` and reading it back. For memory-bound work this eliminates traffic entirely, which is often *more* than the launch-overhead savings.

## Memory-bound vs compute-bound

The split from the [roofline-model](../../Store/roofline-model.md) decides what optimization helps:

- **Memory-bound** (elementwise ops, decode): limited by bandwidth → fusion, quantization, and layout matter; occupancy matters (more in-flight loads).
- **Compute-bound** (matmul, conv): limited by FLOPs → tile sizes, tensor cores, math-level improvements; register pressure is about keeping accumulators alive.

Which one you're in tells you what to change. Most fused pointwise chains are memory-bound; the matmul at the core is compute-bound — that's exactly how [Triton](Triton.md) kernels get tuned differently per case.

## Why it matters

Every DL compiler decision (fusion, tiling, `num_warps`, autotuning) is downstream of this model. When I read generated kernels, "why this grid size / this smem usage" is answered by occupancy; "why did they fuse" by launch overhead + register residency.

## Related

- [Triton](Triton.md) — the programming model that hides this hierarchy.
- [roofline-model](../../Store/roofline-model.md) — memory- vs compute-bound analysis.
- [Fusion-Scheduler](../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md) — the fusion decisions in practice.
- [RegisterAllocation](../IR/RegisterAllocation.md) — the pressure trade-offs in disguise.
