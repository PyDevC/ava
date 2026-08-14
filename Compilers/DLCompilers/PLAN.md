# DLCompilers — Plan

## Goal of this directory

Cover the **deep learning compiler landscape**: what each compiler does, how it's architected, and how they differ. The goal is to be able to pick the right compiler for a job and to understand the shared ideas (graph → IR → lowering → codegen → runtime) that they all implement.

## What's already here

- **README.md** — the shared pipeline (frontend → IR → lowering → tuning → codegen → runtime) and links to each compiler.
- **IREE/** — MLIR-based end-to-end compiler ("eerie"), `.vmfb` artifacts, HAL, backends.
- **TensorRT/** — NVIDIA's: Parser → Builder → Engine → Runtime, precision/fusion, optimization profiles.
- **DISC/** — BladeDISC: graph optimizations + kernel-level, XLA heritage, dynamic shapes.
- **TorchInductor/** — PyTorch's default `torch.compile` backend: Dynamo → FX → loop-level IR → Triton/C++.
- **TVM** — the historical baseline: Relay + TE + AutoTVM/Ansor.
- **XLA/StableHLO** — Google's compiler and the shared frontend IR.
- **ONNX Runtime** — the executor + fusion layer for ONNX models.
- **OpenVINO/TFLite** — Intel and mobile/embedded deployment stacks.
- **Comparison-Matrix** — frontends, IRs, backends, dynamic shapes, open-source, all in one table.

## What to add next

- [x] **TVM** — the historical baseline: Relay (graph IR) + TE (tensor expression) + AutoTVM/Ansor. Worth one note just to see what Inductor's ideas came from.
- [x] **XLA / StableHLO / JAX** — Google's compiler; StableHLO is the shared frontend IR now (IREE consumes it too).
- [x] **ONNX Runtime** — the graph-optimization story for ONNX models; how it differs from full compilers (it's more of an executor + fusion layer).
- [x] **OpenVINO** (Intel), **TFLite/XNNPACK** (mobile) — quick notes for completeness on the deployment side.
- [x] **A comparison matrix** — one note comparing: frontends, IRs, backends, dynamic-shape support, open-source? That comparison is the actual payoff of this directory.
- [x] **MLIR linalg/tosa** — the common lowering target many of these compilers share (see [MLIR](../MLIR/PLAN.md) plan) → covered in [linalg](../MLIR/Dialects/linalg.md) and [tosa-stablehlo](../MLIR/Dialects/tosa-stablehlo.md)
- [ ] **Per-compiler deep dives** to extend existing notes (as I use each one):
  - [ ] IREE: walk a real `.vmfb` compile+run, the HAL dispatch flow.
  - [ ] TensorRT: working through a real engine build with a graph (tensorrt onnx → engine), precision (FP16/INT8) calibration.
  - [ ] DISC: the graph/kernel split and how dynamic shapes are handled.
  - [ ] TorchInductor: actually reading the emitted Triton kernel from a fused model.

## Prioritization

1. **Comparison matrix** — cheap to write, ties everything together.
2. **TVM + XLA/StableHLO** — the two most important missing compilers for context.
3. **ONNX Runtime** — because so many production models go through ONNX.
4. Then per-compiler hands-on deep dives as I use each one.
