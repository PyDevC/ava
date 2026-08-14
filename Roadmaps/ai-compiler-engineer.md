# AI Compiler Engineer

AI compiler engineers build the **software that turns ML models into fast machine code**: graph optimization, kernel generation, and runtime. This is where ML meets classical compilers — the hottest area of ML systems.

## Core skills

- **Compiler fundamentals**: IRs, SSA (see [Dominators](../Compilers/IR/SSA/Dominators.md)), dataflow analysis, loop transformations, codegen, instruction selection.
- **MLIR** — the modern IR framework for ML compilers (see [CMake_Guide](../Compilers/MLIR/CMake_Guide.md), [Affine](../Compilers/MLIR/Dialects/Affine.md)).
- **Deep learning compiler stack**: a graph-level IR → lowering → kernel-level IR → codegen. Study [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md), [IREE](../Compilers/DLCompilers/IREE/IREE.md), [TensorRT](../Compilers/DLCompilers/TensorRT/TensorRT.md), [DISC](../Compilers/DLCompilers/DISC/DISC.md).
- **Kernel programming**: Triton, CUDA, C++ vectorization (see [triton-compiler-engineer](triton-compiler-engineer.md), [gpu-architect](gpu-architect.md)).
- **Performance analysis**: profiling, roofline model, cache behavior.

## The mental model

1. **Frontend**: take an FX graph / ONNX / StableHLO (model-level IR).
2. **Graph passes**: fusion, dead code elimination, layout decisions.
3. **Kernel IR**: tiled loops, vectorization (affine/vector dialects).
4. **Codegen**: emit Triton/CUDA/C++/assembly for the target.
5. **Runtime**: launch, caching, graphs (CUDA graphs).

Each of these layers is a career in itself.

## Roadmap to get there

1. Strong C++ + Python.
2. Classical compilers: SSA, dominators, liveness, loops (my IR notes).
3. LLVM + MLIR (dialects, passes, lowering).
4. One DL compiler end-to-end (Inductor or IREE are the best OSS choices).
5. GPU programming to understand the codegen target.

## Related

- [ai-framework-engineer](ai-framework-engineer.md) — framework side, overlapping heavily.
- [triton-compiler-engineer](triton-compiler-engineer.md) — the kernel-language specialization.
- [hpc-engineer](hpc-engineer.md) — traditional HPC compilers (OpenMP, loop transforms) feed into this.
