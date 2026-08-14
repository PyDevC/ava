# Reading generated Triton/C++ code

The best way to understand what Inductor actually does is to **look at the code it emits** (see [Inductor-IR](Inductor-IR.md) for what the IR is, [Fusion-Scheduler](Fusion-Scheduler.md) for how kernels get chosen). Hands-on is better than theory: compile a tiny model and read the output.

## Setup

```python
import torch, torch.nn as nn
torch.manual_seed(0)
model = nn.Sequential(nn.Linear(16, 32), nn.ReLU(), nn.Linear(32, 8)).cuda()
compiled = torch.compile(model)
y = compiled(torch.randn(64, 16).cuda())
```

Run with `TORCH_LOGS=output_code python script.py`. The first call triggers compilation and prints the generated kernels. A small MLP like this fuses each `Linear+ReLU` pair into its own Triton kernel (the matmul and the pointwise elementwise op fuse).

## Reading a kernel

```python
@triton.jit
def triton_poi_fused__native_batch_norm_legit_0(in_ptr0, out_ptr0, xnumel, XBLOCK: tl.constexpr):
    xnumel = 64
    xoffset = tl.program_id(0) * XBLOCK
    xindex = xoffset + tl.arange(0, XBLOCK)[:]
    xmask = xindex < xnumel
    x0 = xindex
    tmp0 = tl.load(in_ptr0 + x0, eviction_policy='evict_last', xmask=xmask)
    tmp1 = tl.load(in_ptr0 + 16 + x0, xmask=xmask)
    tmp2 = tmp0 + tmp1
    tl.store(out_ptr0 + x0, tmp2, xmask=xmask)
```

Walk through it:

- **Signature**: `triton.jit` decorator, then the buffers (`in_ptr0`, `out_ptr0`), then scalars — `xnumel` is the total number of elements, `XBLOCK: tl.constexpr` is a **compile-time** constant (the block width). Inductor decides `XBLOCK`/`num_warps` during codegen (autotune can vary them).
- **Grid launch**: the GPU launches `ceil(xnumel / XBLOCK)` thread blocks. `tl.program_id(0)` is the block index; `xoffset` is that block's starting element. This kernel handles `[XBLOCK]` elements per block.
- **`tl.arange(0, XBLOCK)`**: the vectorization unit — a range of indices materialized as a "pseudo-tensor" that Triton vectorizes. `tl.load`/`tl.store` load and store those elements.
- **`xmask`**: since `xnumel` (64) isn't a multiple of `XBLOCK` (e.g. 1024), the last block must be masked — that's the `xmask = xindex < xnumel` guard threaded through every load/store.

The fusion shows up literally: `tmp0 + tmp1` is two different inputs summed in one kernel — the "why is it N kernels" question is answered by looking at how many `triton_poi_*` kernels appear. `TORCH_LOGS=inductor` gives the earlier stages (FX graph → IR → schedule); `TORCH_LOGS=scheduler` shows each fusion decision.

## The CPU path

On CPU the same graph lowers to a C++/OpenMP kernel:

```cpp
extern "C" void kernel(double* out_ptr0, const double* in_ptr0)
{
    #pragma omp parallel for
    for (long x0 = 0; x0 < 64; x0++) {
        out_ptr0[x0] = (in_ptr0[x0] + in_ptr0[16 + x0]);
    }
}
```

Same story, no vectorization machinery to read — just a loop over `xnumel` with OpenMP for parallelism.

## Useful config knobs

- `torch._inductor.config.triton.cudagraphs = True` — capture kernels into a CUDA graph so launches replay at ~zero CPU overhead (the "reduce-overhead" mode's trick, see [Integration-Points](../Integration-Points.md)).
- `torch._inductor.config.max_autotune = True` — autotunes block sizes/num_warps (and templates) against your GPU.
- `torch._inductor.config.trace.enabled = True` — dumps the IR schedule to `torch_compile_*` files for the same debugging without parsing logs.

## Why it matters

Reading generated code turns Inductor from a black box into a diagnosable pipeline: you can *see* which ops fused, whether a kernel is memory- or compute-bound from its loads/stores, and whether autotune picked sane block sizes. It's the "look at the assembly" skill, but for ML compilers.

## Related

- [Inductor-IR](Inductor-IR.md) — the loop-level IR this code comes from.
- [Fusion-Scheduler](Fusion-Scheduler.md) — why ops got merged into one kernel or split into many.
- [EntryPoint](EntryPoint.md) — the call chain that reaches codegen.
- [Triton](../../Codegen/Triton.md) — the Triton language/dialect these kernels are written in.
- [TorchInductor](../../../Compilers/DLCompilers/TorchInductor/TorchInductor.md) — the pipeline + autotuning this feeds into.
