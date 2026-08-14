# DISC: Dyanmic Shape Compiler

DISC is an end-to-end dynamic shape compiler build on MLIR infrastructure, it natively supports optimizations for dynamic shapes by introducing first-class feature of shapes in IR. It produces shapes calculation logic for runtime at compile-time which reduces overheads of shape calculations by CPU. It provides opportunities for host-device co-optimization, solves kernel fusion problem with shape propagation.

DISC is now open sourced as **BladeDISC** (github.com/alibaba/BladeDISC) and is one of the key components of Alibaba's PAI-Blade optimization stack.

> NOTE: The name in the paper and the project page stands for "DynamIc Shape Compiler". Paper: https://arxiv.org/abs/2103.05288

## The dynamic shape problem

Major problem with dynamic shapes is lack of fusion, and shape specific optimizations such as proper tilings. The dynamic shape usually occurs in small sized computations such as element-wise and reduction, and most of the large computations are offloaded to computation libraries.

The existing kernel fusion engines could only generate kernels with static shape info inferred during compilation time (this was true when the paper was written, 2021). This can lead to the problem of recompilation for every new input. Historically XLA was a static shape compiler, so it did not support dynamic shapes well and recompiled for every distinct shape combination. Since then there has been work on adding dynamic shape support to XLA (for example experimental bounded dynamic shapes in PyTorch/XLA for TPUs) but it is still far from being a first-class feature there.

There are some workaround solutions that can prevent too much lose of performance from dyanmic shapes:

- We can group static shape ops and dynamic shape ops where we only optimize static shape ops and leave dynamic shape ops as it is, these leads to optimization opportunity loss.
- Another workaround is using padding and slicing that will only recompile if input is larger than padded tensor, but this wastes the resources and could even lead to negative optimizations.

## How DISC solves it

DISC solves the problem by enforcing dynamic shapes in it's High level IR. It adds dynamic shape support on top of the HLO dialect from MLIR (originally the MHLO community dialect; part of the infra has been upstreamed to mlir-hlo). It produces the runtime flow (shape calculation logic) at compile time and compiles it together with the model binary, which allows for host-device co-optimization and removes the shape interpretation overhead at runtime.

- It generates efficient fusion kernels without full shape info.
- It supports various static shape optimizations for the operations where the shape is known, for better performance.
- It supports multiple frontends (TensorFlow, PyTorch via Torch-MLIR) and multiple backends (CUDA, ROCm, x86/CPU).
- Two deployment modes: Plugin mode (embedded in TF/PyTorch runtime, clusters the supported ops) and Standalone mode (AOT compile into a standalone binary).

In their evaluation, BladeDISC shows up to 6.95x speedup compared to PyTorch eager, and comparable or better performance than static optimizing compilers like XLA and TensorRT on dynamic shape workloads.

## References

- Paper: "DISC: A Dynamic Shape Compiler for Machine Learning Workloads" https://arxiv.org/abs/2103.05288
- Code: https://github.com/alibaba/BladeDISC
- Intro post: https://www.alibabacloud.com/blog/bladedisc-a-deep-learning-compiler-open-sourced-by-alibaba_600354
