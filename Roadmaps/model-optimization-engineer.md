# Model optimization engineer

The role that lives **between ML and compilers**: quantization, pruning, distillation, and deployment tuning. It's the practical implementation of [[MachineLearning/deeplearning/model-optimization-for-inference]] as a full-time job.

## Core skills

- **The techniques** (all covered in this repo):
  - Quantization: PTQ/QAT, INT8/FP16/BF16/FP8, per-tensor vs per-channel, calibration ([[MachineLearning/deeplearning/model-optimization-for-inference]], [[MachineLearning/deeplearning/mixed-precision]]).
  - Pruning + sparsity; distillation (student/teacher, [[MachineLearning/deeplearning/regularization-normalization]]'s loss recipe).
  - **The compiler side**: what the serving stack actually does to the model — [[Compilers/DLCompilers/Comparison-Matrix]] (TensorRT/Inductor/ONNX Runtime/IREE), which one fuses what.
- **Benchmarking rigor**: latency/throughput/memory on *target* hardware, warmup, p50/p95, profiling ([[PyTorch/Performance]]). The "does this optimization actually help" discipline.
- **Numerical analysis**: precision sensitivity (which layers tolerate INT8, which need FP16/FP32 — the "sensitive ops stay FP32" rule from [[MachineLearning/mathematical-function/exp-ln-logspace]] and the AMP recipe).
- **Hardware knowledge**: cache, memory bandwidth, tensor cores ([[Store/deep-learning-hardware]]), the hardware model from [[gpu-architect]].

## A typical day

Measure a model's baseline, pick the optimization (usually: compile first → FP16 → INT8 + calibration → pruning/distillation if size matters), verify accuracy delta against a reference (the eval discipline in [[MachineLearning/nlp/GPT-evaluation]]), and iterate on the deployment pipeline.

## The roadmap

1. **ML fundamentals**: the training side ([[MachineLearning/deeplearning/PLAN]]) — you must know what the model does before you shrink it.
2. **The compiler stack**: [[Compilers/DLCompilers/PLAN]] — the tooling is where the job happens.
3. **Numerical precision**: mixed precision + quantization theory ([[MachineLearning/mathematical-function/PLAN]]).
4. **Hardware + measurement**: profiling and the hardware memory model.

## How this repo maps

- [[MachineLearning/deeplearning/model-optimization-for-inference]] — the anchor note.
- [[Compilers/DLCompilers/Comparison-Matrix]] — the tool choice.
- [[MachineLearning/mathematical-function/hard-activations-step]] — quantization-friendly activations.
- [[PyTorch/Performance]] — the measurement toolkit.

## Related

- [[ai-compiler-engineer]] — the more compiler-heavy sibling.
- [[ml-engineer]] — the modeling sibling.
- [[gpu-architect]] — the hardware beneath.
- [[triton-compiler-engineer]] — when you write the kernels yourself.
