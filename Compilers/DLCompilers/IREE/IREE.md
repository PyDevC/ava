# IREE: Intermediate Representation Execution Environment

IREE (pronounced "eerie") is an MLIR-based end-to-end compiler and runtime that lowers Machine Learning models to a unified IR. It is designed to scale from datacenter deployments all the way down to mobile and edge devices.

> Code: https://github.com/iree-org/iree — docs: https://iree.dev

## Why IREE exists

Traditional inference runtimes either hand-write kernels per op per backend (huge engineering cost) or target only a narrow slice of hardware. IREE instead treats compilation as a real compiler problem:

- Ahead-of-time compilation: scheduling and execution logic are compiled together into the artifact, so the runtime stays tiny.
- Retargetable: one model, compiled once per target, with no vendor SDK dependency baked into the compiler.
- Supports advanced model features like dynamic shapes, flow control, and streaming, not just simple static graphs.

## The compiler pipeline

1. Import the model from a framework frontend:
   - StableHLO (from JAX/TensorFlow, and torch-mlir for PyTorch)
   - TFLite via TOSA (Tensor Operator Set Architecture, an open standard of common tensor ops in MLIR)
   - ONNX
2. Graph-level optimization in IREE's own dialects: Flow (dispatch creation and global optimization), Stream, HAL, Util, VM.
3. Codegen per target backend, emitting target specific code.

The output is a `.vmfb` bytecode module (e.g. from `iree-compile`), which you load with a small runtime library (`iree-run-module` to run it from the CLI).

## The runtime

The runtime is intentionally small and uses the HAL (Hardware Abstraction Layer) so the same compiled module can run on different devices: `local-sync`, `local-task`, CUDA, Vulkan, etc. Everything is driven through a small VM that calls into HAL driver functions.

## Backends (HAL target backends)

- `llvm-cpu` - LLVM CPU codegen with microkernels (e.g. `znver4` x86 tuning)
- `cuda` - CUDA codegen for NVIDIA GPUs
- `vulkan-spirv` - SPIR-V codegen, runs anywhere Vulkan compute is supported (cross-vendor, great for mobile)
- `metal` - Apple Metal
- `vmvx` - the portable reference backend (an interpreter, useful for validation and JIT)

## Use cases

- On-device / embedded inference where you want a tiny runtime and AOT compiled artifacts
- Vulkan-first deployment (ship SPIR-V, run on any vendor)
- Latency-sensitive datacenter inference (CUDA backend)

## Contents

- [IREE-Compile-Run](IREE-Compile-Run.md) — the hands-on compile+run walkthrough
- [IREE-HAL-Dispatch](IREE-HAL-Dispatch.md) — the runtime's dispatch flow
- [IREE-Backends](IREE-Backends.md) — llvm-cpu vs CUDA vs Vulkan
- [IREE-vs-Inductor](IREE-vs-Inductor.md) — how IREE compares to `torch.compile`'s backend

## References

- Repo: https://github.com/iree-org/iree
- "Unveiling the Inner Workings of IREE" talk: https://www.youtube.com/watch?v=a3T74I9gGH8
