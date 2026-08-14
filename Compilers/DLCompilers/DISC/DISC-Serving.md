# DISC in Alibaba's serving stack

DISC isn't an academic toy — it ships inside Alibaba's inference stack as part of **PAI-Blade** (the optimization layer of Alibaba Cloud's PAI platform). This note is where it actually runs and why the design choices make sense there.

## Where it fits

- **PAI / BladeDISC serving**: PAI-Blade is the umbrella; DISC is the compiler inside it that turns trained models (PyTorch/TensorFlow, and ONNX via TorchScript import) into optimized inference binaries. Models come from training platforms, get compiled by DISC, and are served behind a model-serving runtime.
- **Plugin vs standalone modes** (from [DISC](DISC.md)): plugin mode slots into the framework's runtime for JIT-style compilation during training/serving startup; standalone mode AOT-compiles a binary for deployment. Serving mostly uses standalone for the latency-critical path.

## The interference problem

The big serving reality: **many models share the same GPU**, and naive batching makes them interfere — one model's kernels fragment the GPU and stall another's. DISC's contribution on this axis:

- Predictable, shape-dispatch-free kernels (see [Dynamic shapes](DISC-Dynamic-Shapes.md)) keep each model's execution steady.
- The runtime does **batch inference** — merging concurrent requests of similar shape into one batch, which is where DISC's dynamic-shape handling pays off (no recompile when the batch size shifts between 8 and 16).
- Model scheduling / GPU partitioning on top keeps co-located models from thrashing each other's caches.

## Targets: GPU and MLU

- **GPU (NVIDIA)**: the primary target — CUDA codegen, the deployment that pays the bills.
- **MLU (Cambricon)**: Cambricon's MLU (machine learning units, used heavily in Chinese datacenters) is a second-class-but-real target — DISC lowers the same kernel IR to it. That's the advantage of the compiler design: a new backend is a new codegen path, not a rewrite (same idea as IREE's backends, see [IREE-Backends](../IREE/IREE-Backends.md)).
- **ROCm / x86 CPU**: also supported, less central.

## Why it matters

DISC is not "a compiler that handles dynamic shapes" in the abstract — it's a compiler whose design was shaped by *mixed dynamic workloads sharing GPUs in a datacenter*. The serving context explains the design (symbols + dispatch, batch-friendly codegen, multi-target) better than the paper does.

## Related

- [DISC](DISC.md) — the project overview.
- [Two-level design](DISC-Two-Level-Design.md) — the compiler internals this runs.
- [Dynamic shapes](DISC-Dynamic-Shapes.md) — why it can batch without recompiling.
- [Comparison-Matrix](../Comparison-Matrix.md) — DISC vs the other compilers.
