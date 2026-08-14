# Where I am now — the mapping

The payoff of the roadmaps: map what **this repo already covers** onto the roles, and see the gaps. Each checkbox below = notes exist in this KB (not "I've mastered it").

## Compiler / systems roles

**AI compiler engineer** ([[ai-compiler-engineer]])
- [x] Compiler fundamentals: IR, SSA, passes, regalloc → [[Compilers/IR/PLAN]]
- [x] MLIR: ops/regions/passes/DialectConversion → [[Compilers/MLIR/PLAN]]
- [x] DL compilers: Inductor, IREE, TVM, XLA, ORT → [[Compilers/DLCompilers/PLAN]]
- [x] PyTorch compiler stack: Dynamo, AOTAutograd, Inductor → [[Compilers/PyTorchCompiler/PLAN]]
- [ ] Build a dialect end-to-end / contribute a pass to IREE or Inductor (hands-on gap)
- [ ] GPU kernel writing (Triton/CUDA) beyond reading generated code

**AI framework engineer** ([[ai-framework-engineer]])
- [x] PyTorch internals: dispatch, autograd, tensor/impl/storage → [[PyTorch/PLAN]]
- [x] C++ foundations → [[Programming/cpp/PLAN]]
- [x] Build/CI → [[PyTorch/CI_Infra]], [[Programming/cpp/cpp-cmake]]
- [ ] Write a custom op (CPU+CUDA) and get it merged (hands-on gap)
- [ ] Real profiler-driven performance work on a framework

**Triton compiler engineer** ([[triton-compiler-engineer]])
- [x] The ideas (loop-level IR, tiling, vectorization) → [[Compilers/PyTorchCompiler/TorchInductor/Inductor-IR]]
- [ ] Actually write Triton kernels by hand (hands-on gap)

## ML roles

**ML engineer** ([[ml-engineer]])
- [x] Algorithms + math toolbox → [[MachineLearning/algorithms/PLAN]], [[MachineLearning/mathematical-function/PLAN]]
- [x] Deep learning: backprop, optimizers, normalization → [[MachineLearning/deeplearning/PLAN]]
- [x] AutoML/tuning → [[MachineLearning/automl/PLAN]]
- [x] NLP/CV applications → [[MachineLearning/nlp/PLAN]], [[MachineLearning/computer-vision/PLAN]]
- [ ] Ship a complete trained+deployed model to production (hands-on gap)

**LLM engineer** ([[llm-engineer]])
- [x] RAG, prompting, agents, fine-tuning theory → [[MachineLearning/nlp/PLAN]]
- [x] Eval methodology → [[MachineLearning/nlp/GPT-evaluation]]
- [ ] Build a production RAG/agent system end-to-end (hands-on gap)

**Model optimization engineer** ([[model-optimization-engineer]])
- [x] Quantization/pruning/distillation theory → [[MachineLearning/deeplearning/model-optimization-for-inference]]
- [x] Compiler tooling → [[Compilers/DLCompilers/Comparison-Matrix]]
- [ ] Do an INT8 QAT + TensorRT deployment benchmark (hands-on gap)

## Hardware / infra roles

**GPU architect / ROCm / HPC** ([[gpu-architect]], [[rocm-developers]], [[hpc-engineer]])
- [x] Systems/parallelism concepts → [[Programming/cpp/PLAN]], [[PyTorch/Distributed-Collectives]]
- [x] Distributed training → [[MachineLearning/deeplearning/distributed-training]]
- [ ] Write and benchmark HIP/CUDA kernels (hands-on gap)

**MLOps / backend** ([[mlops-engineer]], [[backend-infra-engineer]])
- [x] Build/deploy/CI fundamentals → [[Programming/PLAN]], [[OpenSource/PLAN]]
- [ ] Operating a real serving stack (Triton/K8s) (hands-on gap)

## The gaps, summed up

1. **Hands-on kernel/dialect work** — the single biggest gap for the compiler roles.
2. **Production deployment experience** — serving, monitoring, rollout.
3. **Custom op contribution** — the PyTorch contribution path is fully mapped in notes, not yet executed.

## Related

- Each roadmap note in this directory.
- The per-directory [[PLAN]] files are the "next action" versions of this map.
