# Model optimization engineer

The role that lives **between ML and compilers**: quantization, pruning, distillation, and deployment tuning. It's the practical implementation of [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md) as a full-time job.

## Core skills

- **The techniques** (all covered in this repo):
  - Quantization: PTQ/QAT, INT8/FP16/BF16/FP8, per-tensor vs per-channel, calibration ([model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md), [mixed-precision](../MachineLearning/deeplearning/mixed-precision.md)).
  - Pruning + sparsity; distillation (student/teacher, [regularization-normalization](../MachineLearning/deeplearning/regularization-normalization.md)'s loss recipe).
  - **The compiler side**: what the serving stack actually does to the model — [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md) (TensorRT/Inductor/ONNX Runtime/IREE), which one fuses what.
- **Benchmarking rigor**: latency/throughput/memory on *target* hardware, warmup, p50/p95, profiling ([Performance](../PyTorch/Performance.md)). The "does this optimization actually help" discipline.
- **Numerical analysis**: precision sensitivity (which layers tolerate INT8, which need FP16/FP32 — the "sensitive ops stay FP32" rule from [exp-ln-logspace](../MachineLearning/mathematical-function/exp-ln-logspace.md) and the AMP recipe).
- **Hardware knowledge**: cache, memory bandwidth, tensor cores ([deep-learning-hardware](../Store/deep-learning-hardware.md)), the hardware model from [gpu-architect](gpu-architect.md).

## A typical day

Measure a model's baseline, pick the optimization (usually: compile first → FP16 → INT8 + calibration → pruning/distillation if size matters), verify accuracy delta against a reference (the eval discipline in [GPT-evaluation](../MachineLearning/nlp/GPT-evaluation.md)), and iterate on the deployment pipeline.

## The roadmap

1. **ML fundamentals**: the training side ([PLAN](../MachineLearning/deeplearning/PLAN.md)) — you must know what the model does before you shrink it.
2. **The compiler stack**: [PLAN](../Compilers/DLCompilers/PLAN.md) — the tooling is where the job happens.
3. **Numerical precision**: mixed precision + quantization theory ([mathematical-function](../MachineLearning/mathematical-function/)).
4. **Hardware + measurement**: profiling and the hardware memory model.

## How this repo maps

- [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md) — the anchor note.
- [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md) — the tool choice.
- [hard-activations-step](../MachineLearning/mathematical-function/hard-activations-step.md) — quantization-friendly activations.
- [Performance](../PyTorch/Performance.md) — the measurement toolkit.

## Related

- [ai-compiler-engineer](ai-compiler-engineer.md) — the more compiler-heavy sibling.
- [ml-engineer](ml-engineer.md) — the modeling sibling.
- [gpu-architect](gpu-architect.md) — the hardware beneath.
- [triton-compiler-engineer](triton-compiler-engineer.md) — when you write the kernels yourself.
