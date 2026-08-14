# Deep learning hardware

Hardware used to train and inference deep learning models, and how each type executes models and interacts with DL frameworks.

## General-purpose hardware: GPUs

GPUs achieve high parallelism via high memory bandwidth and a high core count. After the Volta architecture, NVIDIA GPUs added **tensor cores** for efficient matrix/matrix-multiply at scale (instead of running tensor ops on generic ALUs). Vendors ship acceleration libraries on top of the raw hardware:

- NVIDIA: cuDNN, TensorRT, NCCL
- AMD: ROCm, MIOpen, RCCL
- Generic/portable: oneAPI, OpenCL/Vulkan

See the [gpu-spec-catalog](gpu-spec-catalog.md) for concrete hardware numbers and [roofline-model](roofline-model.md) for reasoning about whether a workload is bandwidth- or compute-bound on these chips.

## Dedicated hardware: accelerators

Specialized chips built for DL compute, either from startups or big vendors:

- NVIDIA — tensor cores (consumer→datacenter), plus the whole CUDA software stack
- Google — **TPUs**: matrix-multiply unit (MXU), unified buffer (UB), activation unit (AU), driven with CISC instructions by the host CPU
- Intel — NNP (Nervana), Gaudi
- Qualcomm — Cloud AI 100 (and NPUs in phones)
- AWS — Inferentia/Trainium
- Alibaba — Hanguang

The tradeoff vs GPUs: higher efficiency per watt per dollar on the specific op mix you design for, but a much smaller software ecosystem and tight coupling to the vendor's compiler (XLA/StableHLO on TPU, custom stacks on the others).

## Related

- [gpu-spec-catalog](gpu-spec-catalog.md) — the spec sheet.
- [roofline-model](roofline-model.md) — how to decide what the hardware is good at.
- [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md) — what you do to fit on this hardware.
