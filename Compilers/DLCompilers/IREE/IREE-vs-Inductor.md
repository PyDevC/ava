# IREE vs TorchInductor for PyTorch models

Both can compile the same PyTorch model. They are different answers to "make this fast", and which one you want depends on the deployment. **torch-mlir** is the bridge: it traces a PyTorch module into StableHLO, which IREE consumes.

## The two paths

```
PyTorch model
   ├── torch.compile() → Dynamo → AOTAutograd → Inductor → Triton/C++   (in-process)
   └── torch-mlir → StableHLO → IREE → .vmfb → HAL → any backend        (AOT artifact)
```

- **Inductor** lives inside the PyTorch process: it reads the live FX graph, generates Triton/C++, and hands back compiled functions. No separate artifact, no runtime install.
- **IREE** is a real compiler pipeline: trace to StableHLO once, compile AOT to a `.vmfb`, run it with a small runtime that doesn't need PyTorch at all. See [Compile and run](IREE-Compile-Run.md).

## Dynamic shapes

- **Inductor**: symbolic shapes via SymPy (see [SymPy symbolic shapes](../../PyTorchCompiler/TorchDynamo/SymPy-Symbolic-Shapes.md)), guards gate recompiles. Very dynamic-friendly: recompile only when a guard breaks.
- **IREE/torch-mlir**: dynamic shapes are first-class in linalg, and the runtime handles shape dispatch per call. In practice IREE's dynamic-shape path is more conservative about fusion; Inductor fuses more aggressively for PyTorch-native workloads.

## Fusion aggressiveness

- **Inductor** is built for PyTorch's op mix: pointwise/reduction fusion is its core competence, plus cuDNN/oneDNN/CUTLASS templates for conv/matmul (see [Behaviour with things installed](../../PyTorchCompiler/TorchInductor/BehaviourOfTorchInductorWithDifferentThingsInstalled.md)). For a typical PyTorch model, Inductor's fusion is hard to beat because it fuses *your exact* pattern.
- **IREE** has solid linalg fusion, but the torch-mlir import loses some PyTorch-specific context (aliasing, strides) — the graph it sees is a more generic stablehlo. Sometimes it fuses differently/better, sometimes worse.

## Backend coverage

- **Inductor**: Triton (NVIDIA GPU) and C++/OpenMP (CPU). That's it — adding a backend means writing codegen.
- **IREE**: llvm-cpu, CUDA, Vulkan/SPIR-V, ROCm, Metal, WebGPU (see [Backends](IREE-Backends.md)). One model, many targets.

## Maturity for PyTorch

Inductor is the default `torch.compile` — battle-tested on the PyTorch op set, correct for almost everything. torch-mlir + IREE is a stronger compiler but a weaker PyTorch frontend: op-coverage gaps, more export-time surprises, and you lose in-process dynamic behavior.

## When to pick which

- **PyTorch, in-process, want speed now**: `torch.compile` (Inductor).
- **Need a portable/small runtime, non-NVIDIA targets, or a separate serving artifact**: IREE via torch-mlir.
- **NVIDIA GPU, hard latency targets**: benchmark both. Inductor usually wins on GPU-heavy PyTorch models; IREE wins when artifact portability matters more.

## Why it matters

These are the two realistic "what do I do with this PyTorch model" options, and they're philosophically opposite (in-process vs AOT artifact). Knowing which axis matters for a deployment decides the choice in ten seconds.

## Related

- [TorchInductor](../TorchInductor/TorchInductor.md) — the in-process path.
- [Inductor-IR](../../PyTorchCompiler/TorchInductor/Inductor-IR.md) — what Inductor's fusions look like.
- [Comparison-Matrix](../Comparison-Matrix.md) — the whole landscape in one table.
