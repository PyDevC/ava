# TensorRT — Plan

## Goal

Note on **TensorRT** (NVIDIA's inference compiler/runtime): the engine-building flow, optimization techniques (fusion, precision, memory), and architecture-specific engines.

## What's here

- [TensorRT](TensorRT.md) — overview: Parser → Builder → Engine → Runtime, optimizations, FP16/INT8/FP8, optimization profiles, architecture-specific engines, TensorRT-LLM.

## What to add next (see also [PLAN](../PLAN.md))

- [ ] Hands-on: build a real engine from an ONNX model (trtexec / Python API)
- [ ] Precision calibration walkthrough (INT8: calibration sets, sensitivity)
- [ ] Optimization profiles + dynamic shapes in practice
- [ ] TensorRT-LLM vs vanilla TensorRT — when to reach for which
- [ ] Comparison vs IREE/Inductor for the same workload
