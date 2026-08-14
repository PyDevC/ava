# Where I am now — the mapping

The payoff of the roadmaps: map what **this repo already covers** onto the roles, and see the gaps. Each checkbox below = notes exist in this KB (not "I've mastered it").

## Compiler / systems roles

**AI compiler engineer** ([ai-compiler-engineer](ai-compiler-engineer.md))
- [x] Compiler fundamentals: IR, SSA, passes, regalloc → [PLAN](../Compilers/IR/PLAN.md)
- [x] MLIR: ops/regions/passes/DialectConversion → [PLAN](../Compilers/MLIR/PLAN.md)
- [x] DL compilers: Inductor, IREE, TVM, XLA, ORT → [PLAN](../Compilers/DLCompilers/PLAN.md)
- [x] PyTorch compiler stack: Dynamo, AOTAutograd, Inductor → [PLAN](../Compilers/PyTorchCompiler/PLAN.md)
- [ ] Build a dialect end-to-end / contribute a pass to IREE or Inductor (hands-on gap)
- [ ] GPU kernel writing (Triton/CUDA) beyond reading generated code

**AI framework engineer** ([ai-framework-engineer](ai-framework-engineer.md))
- [x] PyTorch internals: dispatch, autograd, tensor/impl/storage → [PLAN](../PyTorch/PLAN.md)
- [x] C++ foundations → [PLAN](../Programming/cpp/PLAN.md)
- [x] Build/CI → [CI_Infra](../PyTorch/CI_Infra.md), [cpp-cmake](../Programming/cpp/cpp-cmake.md)
- [ ] Write a custom op (CPU+CUDA) and get it merged (hands-on gap)
- [ ] Real profiler-driven performance work on a framework

**Triton compiler engineer** ([triton-compiler-engineer](triton-compiler-engineer.md))
- [x] The ideas (loop-level IR, tiling, vectorization) → [Inductor-IR](../Compilers/PyTorchCompiler/TorchInductor/Inductor-IR.md)
- [ ] Actually write Triton kernels by hand (hands-on gap)

## ML roles

**ML engineer** ([ml-engineer](ml-engineer.md))
- [x] Algorithms + math toolbox → [algorithms](../MachineLearning/algorithms/), [mathematical-function](../MachineLearning/mathematical-function/)
- [x] Deep learning: backprop, optimizers, normalization → [PLAN](../MachineLearning/deeplearning/PLAN.md)
- [x] AutoML/tuning → [automl](../MachineLearning/automl/)
- [x] NLP/CV applications → [nlp](../MachineLearning/nlp/), [PLAN](../MachineLearning/computer-vision/PLAN.md)
- [ ] Ship a complete trained+deployed model to production (hands-on gap)

**LLM engineer** ([llm-engineer](llm-engineer.md))
- [x] RAG, prompting, agents, fine-tuning theory → [nlp](../MachineLearning/nlp/)
- [x] Eval methodology → [GPT-evaluation](../MachineLearning/nlp/GPT-evaluation.md)
- [ ] Build a production RAG/agent system end-to-end (hands-on gap)

**Model optimization engineer** ([model-optimization-engineer](model-optimization-engineer.md))
- [x] Quantization/pruning/distillation theory → [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md)
- [x] Compiler tooling → [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md)
- [ ] Do an INT8 QAT + TensorRT deployment benchmark (hands-on gap)

## Hardware / infra roles

**GPU architect / ROCm / HPC** ([gpu-architect](gpu-architect.md), [rocm-developers](rocm-developers.md), [hpc-engineer](hpc-engineer.md))
- [x] Systems/parallelism concepts → [PLAN](../Programming/cpp/PLAN.md), [Distributed-Collectives](../PyTorch/Distributed-Collectives.md)
- [x] Distributed training → [distributed-training](../MachineLearning/deeplearning/distributed-training.md)
- [ ] Write and benchmark HIP/CUDA kernels (hands-on gap)

**MLOps / backend** ([mlops-engineer](mlops-engineer.md), [backend-infra-engineer](backend-infra-engineer.md))
- [x] Build/deploy/CI fundamentals → [PLAN](../Programming/PLAN.md), [OpenSource](../OpenSource/)
- [ ] Operating a real serving stack (Triton/K8s) (hands-on gap)

## The gaps, summed up

1. **Hands-on kernel/dialect work** — the single biggest gap for the compiler roles.
2. **Production deployment experience** — serving, monitoring, rollout.
3. **Custom op contribution** — the PyTorch contribution path is fully mapped in notes, not yet executed.

## Related

- Each roadmap note in this directory.
- The per-directory [PLAN](PLAN.md) files are the "next action" versions of this map.
