# Reading Inductor's generated code

The hands-on skill: knowing what Inductor actually emitted, from the Python-level Triton source down to PTX. The tools are `TORCH_LOGS` for the kernel source and `TRITON_KERNEL_DUMP` for the compiled artifacts.

## Getting the kernel source

```bash
TORCH_LOGS=output_code python my_model.py
```

`output_code` makes Inductor print every generated kernel it emits — the Triton (GPU) or C++ (CPU) source, plus the wrapper that launches it. On a cache hit the generated source is also in `~/.cache/torch/inductor` as `.py` files — grep those for `@triton.jit`. To force dumping even for cached modules:

```python
torch._inductor.config.trace.enabled = True
```

## Getting the compiled cubin / PTX

Triton compiles the Triton source to LLVM IR → PTX → SASS (cubin). To see it:

```bash
TRITON_KERNEL_DUMP=1 python my_model.py     # dumps PTX + cubin + LLVM IR to disk
```

or in Python:

```python
torch._inductor.config.triton.dump_ir = True     # save the Triton -> LLVM -> PTX stages
```

The dumps land in a `__triton_dump/`-style directory keyed by kernel hash. Also `torch._inductor.config.trace.debug` shows the scheduling decisions before codegen.

## Walking a tiny example: elementwise add

Say the model is literally `x + y`. Inductor fuses nothing (nothing to fuse) and emits one kernel:

```python
@triton.jit
def triton_poi_fused_add_0(in_ptr0, in_ptr1, out_ptr0, xnumel, XBLOCK: tl.constexpr):
    xnumel = tl.full([1], xnumel, tl.int32)
    xoffset = tl.program_id(0) * XBLOCK
    xindex = xoffset + tl.arange(0, XBLOCK)
    xmask = xindex < xnumel
    x0 = xindex
    tmp0 = tl.load(in_ptr0 + x0, mask=xmask)
    tmp1 = tl.load(in_ptr1 + x0, mask=xmask)
    tmp2 = tmp0 + tmp1
    tl.store(out_ptr0 + x0, tmp2, mask=xmask)
```

Line by line:

- **Signature** — `in_ptr0, in_ptr1, out_ptr0` are the tensor data pointers; `xnumel` is the total element count; `XBLOCK` is a `tl.constexpr` block size chosen by autotune/heuristic.
- **`tl.program_id(0) * XBLOCK`** — this program owns a contiguous chunk of the flattened tensor; `tl.arange(0, XBLOCK)` builds the vector of offsets. This is the [CUDA-Kernel-Launch](CUDA-Kernel-Launch.md) grid mapping, written tile-style.
- **`xmask = xindex < xnumel`** — the tail mask: when the size isn't a multiple of `XBLOCK`, the last block is partly out of bounds, so loads/stores are masked. Inductor emits it whenever `xnumel` isn't provably a multiple of the block.
- **`tl.load`/`tl.store`** — block loads/stores; Triton coalesces them into vectorized loads (that's when it can pick `ld.global.v4` in PTX).
- **`tmp0 + tmp1`** — one vector add; the whole kernel is the "loop" over the tile. There's no explicit loop over elements — the block *is* the parallelism.

## Why the generated code looks like this

Everything in the kernel answers a prior decision: one block per contiguous slice (coalescing), masked tail (dynamic shapes), vectorized ops (bandwidth), no shared memory (no reuse to stage), `XBLOCK` fixed at compile time (the autotuner benchmarked it, or the heuristic picked 1024). The C++ CPU path is the same story with `#pragma omp` loops instead of `tl.arange`. On a bigger graph you see *why it became N kernels* — each separate `@triton.jit` function is a fusion decision from the [scheduler](../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md).

## Related

- [Triton](Triton.md) — the language being read.
- [CUDA-Kernel-Launch](CUDA-Kernel-Launch.md) — the hardware model behind masks/blocks/occupancy.
- [Inductor-IR](../PyTorchCompiler/TorchInductor/Inductor-IR.md) — the IR that generated this source.
- [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md) — the pipeline end to end.
