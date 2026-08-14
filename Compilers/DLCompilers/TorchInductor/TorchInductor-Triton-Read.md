# Reading the Triton kernels Inductor generates

The hands-on item: actually *look* at a generated Triton kernel and understand it. `torch.compile` is not a black box — it prints the kernel source, and once you can read one kernel you can read the whole model's compiled form.

## Get a kernel printed

A tiny model and the log flag:

```python
# mini.py
import torch

@torch.compile
def f(x, y):
    return torch.relu(x + y) * 2

f(torch.randn(1024, 1024), torch.randn(1024, 1024))
```

```bash
TORCH_LOGS=output_code python mini.py
```

`TORCH_LOGS=output_code` prints the generated Triton source for every kernel, plus the wrapper code that calls them. (Alternative: `torch._inductor.config.trace.enabled = True`.)

## The generated kernel, decoded

The pointwise `relu(x+y)*2` becomes one Triton kernel. Here's what the parts mean:

```python
@triton.jit
def triton_poi_fused_mul_relu(in_ptr0, in_ptr1, out_ptr0, XBLOCK: tl.constexpr):
    xnumel = 1048576                      # total elements (1024*1024)
    xoffset = tl.program_id(0) * XBLOCK   # which block THIS program does
    xindex = xoffset + tl.arange(0, XBLOCK)  # [XBLOCK] consecutive indices
    xmask = xindex < xnumel               # guard for the tail (partial block)
    tmp0 = tl.load(in_ptr0 + xindex, mask=xmask)   # load x
    tmp1 = tl.load(in_ptr1 + xindex, mask=xmask)   # load y
    tmp2 = tmp0 + tmp1                    # x + y
    tmp3 = tl.maximum(tmp2, 0.0)          # relu
    tmp4 = tmp3 * 2                       # * 2 (fused, no temp tensor)
    tl.store(out_ptr0 + xindex, tmp4, mask=xmask)  # store result
```

- **kernel signature** `(in_ptr0, in_ptr1, out_ptr0, ...)` — the pointers, plus `tl.constexpr` compile-time constants (tile sizes). No shapes as runtime args — grid + XBLOCK encode the loop.
- **grid** (in the wrapper): `(triton.cdiv(1048576, XBLOCK),)`. `cdiv` = ceil-div: the number of blocks needed. `program_id(0)` is the block index; `xoffset = program_id(0) * XBLOCK` is that block's start.
- **`tl.arange(0, XBLOCK)`** — a vector of indices `[0, XBLOCK)`; the vector width = parallelism inside the block.
- **`xmask = xindex < xnumel`** — tail handling: when `xnumel` isn't a multiple of `XBLOCK`, the last block partially covers it; masked lanes no-op. Needed with dynamic shapes — this is where symbolic shapes flow in.
- **`tl.load` / `tl.store`** — vectorized load/store with optional mask. Consecutive threads touch consecutive addresses, so accesses coalesce.
- The ops are inlined: no separate kernels for `+`, `relu`, `*2` — that's the scheduler's fusion (see [Fusion-Scheduler](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md)) made visible.

A matmul kernel instead ends with **`tl.dot(a, b)`** (the MMA path) plus an epilogue `tl.load`/`tl.store` for the output tile, and the wrapper passes **`num_warps`** (e.g. 4/8), which sets the block thread count.

## Why it matters

Reading kernels is the fastest debugging loop in compiled PyTorch:

- "Why N kernels?" → count the `triton_poi_*` functions printed.
- "Why slow?" → check for unmasked whole-tensor loops that should have fused, or a tail-mask everywhere (shape not a multiple of the block → padding wins).
- Confirms what Inductor actually fused vs what I *thought* it fused — the ground truth the scheduler's `score_fusion` produced.

## Related

- [TorchInductor](TorchInductor.md) — the pipeline that emits these.
- [Triton](../../Codegen/Triton.md) — the kernel language these are written in.
- [Inductor-IR](../../PyTorchCompiler/TorchInductor/Inductor-IR.md) — the loop-level IR these kernels come from.
- [Fusion-Scheduler](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md) — why this chain fused into one kernel.
- [SymPy symbolic shapes](../../PyTorchCompiler/TorchDynamo/SymPy-Symbolic-Shapes.md) — where the tail-mask/grid sizes come from.
- [linalg](../../MLIR/Dialects/linalg.md) — the MLIR-world analogue of the same loop nest.
