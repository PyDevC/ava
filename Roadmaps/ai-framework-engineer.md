# AI Framework Engineer

AI framework engineers work **on the frameworks themselves** (PyTorch, TensorFlow, JAX, Triton, IREE): kernels, compilers, distributed runtime, Python bindings, and performance. It's compiler + systems + ML all at once.

## Core skills

- **C++** deeply (RAII, templates, memory, SIMD); **Python** for bindings/interfaces (see [bytecode](../Programming/python/core/bytecode.md) for the execution model).
- **Compilers**: IRs, SSA, lowering, codegen (my notes: [DLCompilers](../Compilers/DLCompilers/), [Dominators](../Compilers/IR/SSA/Dominators.md), [Affine](../Compilers/MLIR/Dialects/Affine.md)).
- **GPU/CPU programming**: CUDA/ROCm, vectorization, memory hierarchies. (See [gpu-architect](gpu-architect.md) and [rocm-developers](rocm-developers.md).)
- **PyTorch internals**: ATen/`c10`, dispatch, autograd, `torch.compile` stack (see [Explore](../PyTorch/Explore.md), [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md)).
- **Performance analysis**: profilers (nsight, perf), benchmarking, flame graphs.

## Typical day-to-day

- Writing/optimizing a kernel (Triton or raw CUDA), fixing a miscompile in a pass, making a tensor subclass dispatch correctly.
- Reading PTX/SASS/assembly, running micro-benchmarks, arguing about memory layout.
- Contributing to open-source frameworks (PyTorch is the big one).

## Roadmap to get there

1. C++ (strong) + Python.
2. Compiler fundamentals: SSA, loops, codegen (start with my [Compilers](../Compilers/) notes).
3. One GPU programming model: CUDA (then ROCm if you want AMD).
4. Deep-dive a real framework: PyTorch — understand dispatch → autograd → Dynamo → inductor.
5. Pick a specialty: operator kernels, graph compilation, distributed runtime, MLIR dialect work.

## Related

- [ai-compiler-engineer](ai-compiler-engineer.md) — the compiler half, without the framework-API half.
- [triton-compiler-engineer](triton-compiler-engineer.md) — Triton-specific path.
- [pytorch-developers](pytorch-developers.md) — the PyTorch contribution path.
