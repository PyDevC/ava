# IREE — Plan

## Goal

Note on **IREE** (the MLIR end-to-end compiler): the compile flow, `.vmfb` artifacts, HAL runtime, and the backend story.

## What's here

- [[IREE]] — overview: MLIR-based, "eerie", HAL, backends (llvm-cpu, cuda, vulkan-spirv, metal, vmvx), StableHLO/TOSA/ONNX frontends.

## What to add next (see also [[../PLAN]])

- [ ] Walk a real compile+run: model → `.vmfb` → HAL execution
- [ ] The HAL dispatch flow (buffers, dispatches, semaphores) in detail
- [ ] Backend comparison: when CUDA vs Vulkan vs llvm-cpu
- [ ] IREE vs TorchInductor for PyTorch models (the `torch-mlir` connection)
