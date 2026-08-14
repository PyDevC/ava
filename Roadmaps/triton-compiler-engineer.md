# Triton Compiler Engineer

Triton is an open-source **GPU kernel language and compiler** (OpenAI): you write Python-like kernels, and Triton generates CUDA/PTX/HIP for you. It's the language PyTorch Inductor emits for GPU codegen — so Triton engineers build both the language and its compiler/runtime.

## Core skills

- **GPU programming fundamentals**: CUDA memory hierarchy (registers, shared, global), warps, occupancy, synchronization (see [gpu-architect](gpu-architect.md)).
- **Python + C++** (Triton's frontend is Python; the compiler core is C++/LLVM).
- **LLVM internals**: Triton lowers through LLVM — target-specific passes, PTX generation.
- **Compiler concepts**: IR (Triton IR), passes, autotuning, tile/block programming model.
- **Benchmarking**: measuring kernel throughput, memory vs compute bound, launch overhead.

## How Triton works (the mental model)

1. Kernel written in Triton language (block-level operations on tiles).
2. Compiler performs: memory coalescing, shared-memory tiling, vectorization, and hoisting — automatically.
3. Emits LLVM IR → PTX (NVIDIA) or GCN (AMD via ROCm) or CUDA.
4. Runtime + JIT cache + autotune for the best config.

This "let the compiler do the low-level work" philosophy is why Inductor chose Triton for GPU codegen.

## Roadmap to get there

1. C++ + Python; CUDA programming (write raw kernels first — you need to know what Triton saves you from).
2. LLVM basics (IR, passes, backends).
3. Write many Triton kernels; study how they lower (Triton has great `--ptx` debugging).
4. Contribute to Triton (OpenAI/triton on GitHub) — kernel language features, autotuning, new targets.
5. Understand the PyTorch side: how Inductor generates Triton from its IR (see [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md)).

## Related

- [ai-compiler-engineer](ai-compiler-engineer.md) — the broader role; Triton is a specialization.
- [ai-framework-engineer](ai-framework-engineer.md) — Inductor's Triton codegen lives here.
- [gpu-architect](gpu-architect.md) — the hardware you're optimizing for.
- [hpc-engineer](hpc-engineer.md) — classic HPC vectorization philosophy, applied to GPUs.
