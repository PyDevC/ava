# Triton

Triton is a **GPU kernel language + compiler** (OpenAI): you write Python-like kernels that operate on *tiles* (blocks of data), and the compiler figures out the CUDA-level details — thread indexing, memory coalescing, shared-memory staging, vectorization — for you. It's what PyTorch Inductor emits for GPU codegen.

## Block-level / program-level kernels

A Triton kernel is a **grid of programs**, not a grid of threads. Each program instance runs the *same* body over a **block of data** (a tile of the tensor). You never index a single thread:

```
@triton.jit
def add_kernel(x, y, out, BLOCK: tl.constexpr):
    pid = tl.program_id(0)                      # which program (block) am I
    offs = pid * BLOCK + tl.arange(0, BLOCK)    # the element range this block owns
    xv = tl.load(x + offs)
    yv = tl.load(y + offs)
    tl.store(out + offs, xv + yv)
```

- `@triton.jit` — the JIT decorator: the function is traced (interpreted by Python) and compiled to a kernel. `BLOCK` is a `tl.constexpr` compile-time constant, so the trace unrolls and vectorizes it.
- `tl.arange(0, BLOCK)` — a vector of consecutive offsets; `tl.load`/`tl.store` read/write a *block* of memory at once.
- The compiler chooses how to map the block across threads/warps (`num_warps`), coalesces the loads, and vectorizes them.

## How it differs from writing CUDA directly

In CUDA you write code that runs *per thread* and do the indexing math yourself (`threadIdx.x + blockIdx.x * blockDim.x`), worry about warp-level behavior, and hand-roll shared-memory use. Triton inverts this: the *program* is the unit, tiles are the data, and the compiler does the thread-level bookkeeping. The win: kernels read like the math they implement and are portable — the same Triton kernel compiles to PTX (NVIDIA) or GCN (AMD via ROCm).

## Triton IR

Triton lowers the traced kernel to **TritonIR** — an SSA, loop-level IR with tile-typed values: `tt.load`, `tt.store`, `tt.addptr`, `tl.dot` (block matmul), reductions. Its shape is exactly what Inductor's loop IR wants to produce (see [Inductor-IR](../PyTorchCompiler/TorchInductor/Inductor-IR.md)): the "loop/tile" level below tensor ops and above scalar machine code. From TritonIR the pipeline goes TritonIR → LLVM IR → PTX → SASS.

## How Inductor generates it

Inductor's GPU backend lowers its own loop IR (pointwise/reduction/template ops) into a Triton kernel string — block sizes chosen by heuristics or autotuning, reductions mapped to `tl.reduce`/atomics, matmul templates emitting `tl.dot`. Each fused node of the [scheduler](../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md) becomes one Triton `@jit` function with a matching grid; the wrapper (see [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md)) launches it with the right grid size.

## Why it matters

Triton is the *de facto* GPU codegen target for DL compilers: high-level enough that Inductor can emit it, low-level enough that kernels are fast without hand-tuning. Reading generated Triton (`TORCH_LOGS=output_code`, see [Reading-Inductor-PTX](Reading-Inductor-PTX.md)) is the fastest way to see what Inductor decided.

## Related

- [CUDA-Kernel-Launch](CUDA-Kernel-Launch.md) — the hardware model Triton abstracts away.
- [Reading-Inductor-PTX](Reading-Inductor-PTX.md) — reading what Triton/Inductor actually emit.
- [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md) — the pipeline that emits Triton.
- [Inductor-IR](../PyTorchCompiler/TorchInductor/Inductor-IR.md) — the loop IR Triton is generated from.
- [triton-compiler-engineer](../../Roadmaps/triton-compiler-engineer.md) — the roadmap this note feeds into.
