# AI Framework Engineer

AI framework engineers work **on the frameworks themselves** (PyTorch, TensorFlow, JAX, Triton, IREE): kernels, compilers, distributed runtime, Python bindings, and performance. It's compiler + systems + ML all at once.

## Core skills

- **C++** deeply (RAII, templates, memory, SIMD); **Python** for bindings/interfaces (see [[Programming/python/core/bytecode]] for the execution model).
- **Compilers**: IRs, SSA, lowering, codegen (my notes: [[Compilers/DLCompilers]], [[Compilers/IR/SSA/Dominators]], [[Compilers/MLIR/Dialects/Affine]]).
- **GPU/CPU programming**: CUDA/ROCm, vectorization, memory hierarchies. (See [[gpu-architect]] and [[rocm-developers]].)
- **PyTorch internals**: ATen/`c10`, dispatch, autograd, `torch.compile` stack (see [[PyTorch/Explore]], [[Compilers/DLCompilers/TorchInductor/TorchInductor]]).
- **Performance analysis**: profilers (nsight, perf), benchmarking, flame graphs.

## Typical day-to-day

- Writing/optimizing a kernel (Triton or raw CUDA), fixing a miscompile in a pass, making a tensor subclass dispatch correctly.
- Reading PTX/SASS/assembly, running micro-benchmarks, arguing about memory layout.
- Contributing to open-source frameworks (PyTorch is the big one).

## Roadmap to get there

1. C++ (strong) + Python.
2. Compiler fundamentals: SSA, loops, codegen (start with my [[Compilers]] notes).
3. One GPU programming model: CUDA (then ROCm if you want AMD).
4. Deep-dive a real framework: PyTorch — understand dispatch → autograd → Dynamo → inductor.
5. Pick a specialty: operator kernels, graph compilation, distributed runtime, MLIR dialect work.

## Related

- [[ai-compiler-engineer]] — the compiler half, without the framework-API half.
- [[triton-compiler-engineer]] — Triton-specific path.
- [[pytorch-developers]] — the PyTorch contribution path.
