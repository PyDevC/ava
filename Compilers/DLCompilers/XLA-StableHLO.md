# XLA and StableHLO

XLA is **Google's deep learning compiler** (JAX and TensorFlow both use it). StableHLO — a portable, versioned dialect of XLA's HLO — has become the shared frontend IR for the whole ecosystem (IREE consumes it too, see [[../MLIR/Dialects/tosa-stablehlo]]).

## The architecture

- **Frontend → HLO** (High-Level Optimizer IR): JAX/TF emit `hlo.*` ops (`hlo.dot`, `hlo.convolution`, `hlo.reduce`) — a high-level, shape-aware, batch-ready op set. StableHLO is the "portable" version of this (a stable spec + versioning so compilers and frontends don't break each other).
- **Optimization**: HLO is aggressively optimized — algebraic simplifications, fusion (a "fusion" op bundles several HLO ops into one kernel, see [[TorchInductor/TorchInductor]]'s fusion for the same idea), layout assignment.
- **Lowering → codegen**: `hlo` → `lmhlo` (low-level HLO, ops on buffers) → XLA's codegen for **GPU (CUDA/HIP), CPU (via LLVM)**, and TPU. The GPU pipeline historically used an LLVM-based "XLA GPU backend"; the modern path is the **XLA GPU via Triton/LMHLO** evolution (XLA now emits Triton kernels too — the Inductor/XLA convergence is real).

## What it's known for

- **Fusion** (kernel fusion) as a first-class optimization — the "fused" ops that made the original attention/BERT graphs fast.
- **Graph-level optimization that PyTorch's eager mode can't do** — this is why `jax.jit` was fast on big models; eager PyTorch re-launches kernels.
- **`XLA:GPU` autotuning** — search over tile sizes/layouts per device; the ancestor of Inductor's autotune (see [[TorchInductor/TorchInductor]]).

## Dynamic shapes — the hard part

XLA historically required static shapes (compile per shape → recompilation storms). Modern XLA (and the StableHLO story) handles dynamic shapes via shape polymorphism — a compile-time shape-symbol per dimension, avoiding per-batch-size recompiles. This is the same dynamic-shape problem DISC and Inductor tackle (see [[../DISC/DISC]]).

## The shared-frontend angle

**StableHLO is the interchange format**: PyTorch (`torch.export` → StableHLO via torch-mlir), JAX, and TensorFlow all emit it; IREE, XLA, and others consume it. Knowing StableHLO's op set means knowing the input contract of almost every modern ML compiler.

## Related

- [[../MLIR/Dialects/tosa-stablehlo]] — the dialect-level view.
- [[../README]] — where XLA sits in the pipeline map.
- [[../TorchInductor/TorchInductor]] — the "compile a PyTorch graph" competitor with convergent ideas.
- [[../MLIR/Dialects/linalg]] — the common lowering target.
