# newBrain — Index

The map of the whole knowledge base. Every subtree's roadmap (`PLAN.md`) has been completed and removed, so the notes under each directory are the content. This file is the overview of what exists and where to start.

## Contents

- [The big picture](#the-big-picture)
- [Compilers](#compilers) — IR, LLVM, MLIR, DL compilers, PyTorch compiler stack, codegen
- [MachineLearning](#machinelearning) — algorithms, math, deep learning, NLP, vision, AutoML, evaluation, MLOps, RL
- [PyTorch](#pytorch) — framework internals
- [Programming](#programming) — C++, Python, shell
- [OpenSource](#opensource) — contributing, PRs, CI
- [Roadmaps](#roadmaps) — career paths mapped to these notes
- [Store](#store) — references: hardware, papers, glossary

## The big picture

This KB sits where my compiler and ML interests meet. The two spines:

1. **How ML models become fast programs** — `Compilers/` (IR → MLIR → DL compilers → the PyTorch compiler stack → codegen).
2. **How ML models are built** — `MachineLearning/` (algorithms → math → deep learning → NLP → vision → AutoML → evaluation → MLOps → RL).

`Roadmaps/where-am-i-now.md` maps which areas the repo covers and where I am. Every subtree's planned topics have been completed and folded into notes.

---

## Compilers

From classic IR passes up to modern MLIR-based compilers and the `torch.compile` stack.

### `Compilers/IR` — classic compiler IR and passes
- [IR-Taxonomy](Compilers/IR/IR-Taxonomy.md) — CFG vs graph, high vs low level, stack vs register
- [Liveness](Compilers/IR/Liveness.md) — live ranges, def-use
- [SSAConstruction](Compilers/IR/SSAConstruction.md) — converting to SSA form
- [PhiElimination](Compilers/IR/PhiElimination.md) — resolving φ-nodes
- [DeadCodeElimination](Compilers/IR/DeadCodeElimination.md) — DCE
- [CommonSubexpressionElimination](Compilers/IR/CommonSubexpressionElimination.md) — CSE
- [CopyPropagation](Compilers/IR/CopyPropagation.md)
- [RegisterAllocation](Compilers/IR/RegisterAllocation.md) — graph-coloring / linear scan
- [LLVM-IR](Compilers/IR/LLVM-IR.md), [LLVM-Backend](Compilers/IR/LLVM-Backend.md), [SSI](Compilers/IR/SSI.md)
- `IR/SSA/` — [Dominators](Compilers/IR/SSA/Dominators.md), [PruneSSA](Compilers/IR/SSA/PruneSSA.md)

### `Compilers/Codegen` — kernel-level codegen
- [Triton](Compilers/Codegen/Triton.md) — block-level kernel programming
- [CUDA-Kernel-Launch](Compilers/Codegen/CUDA-Kernel-Launch.md) — grid/block/occupancy
- [Reading-Inductor-PTX](Compilers/Codegen/Reading-Inductor-PTX.md) — reading the generated Triton/C++/PTX

### `Compilers/MLIR` — the multi-level IR framework
- Core concepts: [Operations-Types-Values](Compilers/MLIR/Operations-Types-Values.md), [Regions](Compilers/MLIR/Regions.md), [Builtin-Dialects](Compilers/MLIR/Builtin-Dialects.md)
- Infrastructure: [Passes](Compilers/MLIR/Passes.md), [DialectConversion](Compilers/MLIR/DialectConversion.md), [Canonicalization](Compilers/MLIR/Canonicalization.md), [CMake-Guide](Compilers/MLIR/CMake-Guide.md)
- Declarative tools: [TableGen](Compilers/MLIR/TableGen.md), [DRR](Compilers/MLIR/DRR.md)
- Learning path: [Toy-Tutorial](Compilers/MLIR/Toy-Tutorial.md) — the Toy language walkthrough, [Build-A-Tiny-Dialect](Compilers/MLIR/Build-A-Tiny-Dialect.md) — the hands-on recipe
- `Dialects/`: [scf-vector](Compilers/MLIR/Dialects/scf-vector.md), [linalg](Compilers/MLIR/Dialects/linalg.md), [Tensor-memref](Compilers/MLIR/Dialects/Tensor-memref.md), [tosa-stablehlo](Compilers/MLIR/Dialects/tosa-stablehlo.md), [LLVM-Dialect](Compilers/MLIR/Dialects/LLVM-Dialect.md), [Affine](Compilers/MLIR/Dialects/Affine.md)

### `Compilers/DLCompilers` — the ML-focused compiler landscape
- [Comparison-Matrix](Compilers/DLCompilers/Comparison-Matrix.md) — start here: how the tools relate
- [TVM](Compilers/DLCompilers/TVM.md), [XLA-StableHLO](Compilers/DLCompilers/XLA-StableHLO.md), [ONNXRuntime](Compilers/DLCompilers/ONNXRuntime.md), [OpenVINO-TFLite](Compilers/DLCompilers/OpenVINO-TFLite.md)
- `IREE/`: [IREE](Compilers/DLCompilers/IREE/IREE.md), [IREE-Compile-Run](Compilers/DLCompilers/IREE/IREE-Compile-Run.md), [IREE-HAL-Dispatch](Compilers/DLCompilers/IREE/IREE-HAL-Dispatch.md), [IREE-Backends](Compilers/DLCompilers/IREE/IREE-Backends.md), [IREE-vs-Inductor](Compilers/DLCompilers/IREE/IREE-vs-Inductor.md)
- `TensorRT/`: [TensorRT](Compilers/DLCompilers/TensorRT/TensorRT.md), [TensorRT-Engine-Build](Compilers/DLCompilers/TensorRT/TensorRT-Engine-Build.md), [TensorRT-Precision-Calibration](Compilers/DLCompilers/TensorRT/TensorRT-Precision-Calibration.md), [TensorRT-Optimization-Profiles](Compilers/DLCompilers/TensorRT/TensorRT-Optimization-Profiles.md), [TensorRT-LLM](Compilers/DLCompilers/TensorRT/TensorRT-LLM.md)
- `DISC/`: [DISC](Compilers/DLCompilers/DISC/DISC.md), [DISC-Two-Level-Design](Compilers/DLCompilers/DISC/DISC-Two-Level-Design.md), [DISC-Dynamic-Shapes](Compilers/DLCompilers/DISC/DISC-Dynamic-Shapes.md), [DISC-Serving](Compilers/DLCompilers/DISC/DISC-Serving.md)
- `TorchInductor/`: [TorchInductor](Compilers/DLCompilers/TorchInductor/TorchInductor.md) — the compiler-side view

### `Compilers/PyTorchCompiler` — the torch.compile stack
- The pipeline: [Integration-Points](Compilers/PyTorchCompiler/Integration-Points.md)
- [AOTAutograd](Compilers/PyTorchCompiler/AOTAutograd.md), [FX-Graph-IR](Compilers/PyTorchCompiler/FX-Graph-IR.md)
- `TorchDynamo/`: [Trace-Lifecycle](Compilers/PyTorchCompiler/TorchDynamo/Trace-Lifecycle.md), [VariableTracker](Compilers/PyTorchCompiler/TorchDynamo/VariableTracker.md), [Guards](Compilers/PyTorchCompiler/TorchDynamo/Guards.md), [GraphBreaks](Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks.md), [SymPy-Symbolic-Shapes](Compilers/PyTorchCompiler/TorchDynamo/SymPy-Symbolic-Shapes.md), [DebugWithTorchDynamo](Compilers/PyTorchCompiler/TorchDynamo/DebugWithTorchDynamo.md)
- `TorchInductor/`: [EntryPoint](Compilers/PyTorchCompiler/TorchInductor/EntryPoint.md), [Inductor-IR](Compilers/PyTorchCompiler/TorchInductor/Inductor-IR.md), [Fusion-Scheduler](Compilers/PyTorchCompiler/TorchInductor/Fusion-Scheduler.md), [BehaviourOfTorchInductorWithDifferentThingsInstalled](Compilers/PyTorchCompiler/TorchInductor/BehaviourOfTorchInductorWithDifferentThingsInstalled.md)

---

## MachineLearning

### `MachineLearning/algorithms` — the classic ML toolbox
- Regression/classification: [logistic-regression](MachineLearning/algorithms/logistic-regression.md), [classification](MachineLearning/algorithms/classification.md), [neural-network](MachineLearning/algorithms/neural-network.md), [tabular-regression](MachineLearning/algorithms/tabular-regression.md)
- Trees/ensembles: [decision-trees](MachineLearning/algorithms/decision-trees.md), [bagging-random-forests](MachineLearning/algorithms/bagging-random-forests.md), [stacking](MachineLearning/algorithms/stacking.md), [gradient-boosting](MachineLearning/algorithms/gradient-boosting.md), [XGBoost](MachineLearning/algorithms/XGBoost.md), [LightGBM](MachineLearning/algorithms/LightGBM.md)
- Geometric/kernel: [SVM](MachineLearning/algorithms/SVM.md), [k-nearest-neighbors](MachineLearning/algorithms/k-nearest-neighbors.md), [k-means](MachineLearning/algorithms/k-means.md), [SOM](MachineLearning/algorithms/SOM.md), [PCA](MachineLearning/algorithms/PCA.md)
- Probabilistic: [naive-bayes](MachineLearning/algorithms/naive-bayes.md)
- Fundamentals: [bias-variance-tradeoff](MachineLearning/algorithms/bias-variance-tradeoff.md), [regularization](MachineLearning/algorithms/regularization.md), [cross-validation](MachineLearning/algorithms/cross-validation.md), [cost-function](MachineLearning/algorithms/cost-function.md), [loss-function](MachineLearning/algorithms/loss-function.md), [injectivity](MachineLearning/algorithms/injectivity.md), [ranking](MachineLearning/algorithms/ranking.md), [transfer-learning](MachineLearning/algorithms/transfer-learning.md), [imbalanced-classification](MachineLearning/algorithms/imbalanced-classification.md), [model-interpretability](MachineLearning/algorithms/model-interpretability.md), [base-estimator](MachineLearning/algorithms/base-estimator.md), [universal-approximation-function](MachineLearning/algorithms/universal-approximation-function.md), [weighted-linear-regression](MachineLearning/algorithms/weighted-linear-regression.md)

### `MachineLearning/mathematical-function` — the function zoo
- Activations: [relu-function](MachineLearning/mathematical-function/relu-function.md), [tanh-function](MachineLearning/mathematical-function/tanh-function.md), [logistic-function](MachineLearning/mathematical-function/logistic-function.md), [softmax-function](MachineLearning/mathematical-function/softmax-function.md), [logit-function](MachineLearning/mathematical-function/logit-function.md), [hard-activations-step](MachineLearning/mathematical-function/hard-activations-step.md)
- Distributions: [normal-distribution](MachineLearning/mathematical-function/normal-distribution.md), [bernoulli-categorical](MachineLearning/mathematical-function/bernoulli-categorical.md), [uniform-distribution](MachineLearning/mathematical-function/uniform-distribution.md), [quantile-function](MachineLearning/mathematical-function/quantile-function.md), [indicator-function](MachineLearning/mathematical-function/indicator-function.md)
- Geometry/analysis: [norms](MachineLearning/mathematical-function/norms.md), [dot-product-cosine](MachineLearning/mathematical-function/dot-product-cosine.md), [gradient-hessian](MachineLearning/mathematical-function/gradient-hessian.md), [convexity-jensen](MachineLearning/mathematical-function/convexity-jensen.md)
- Transformations: [affine-function](MachineLearning/mathematical-function/affine-function.md), [linear-function](MachineLearning/mathematical-function/linear-function.md), [exp-ln-logspace](MachineLearning/mathematical-function/exp-ln-logspace.md), [lipschitz-function](MachineLearning/mathematical-function/lipschitz-function.md)

### `MachineLearning/deeplearning` — DL foundations
- Learning mechanics: [backpropagation](MachineLearning/deeplearning/backpropagation.md), [regularization-normalization](MachineLearning/deeplearning/regularization-normalization.md), [mixed-precision](MachineLearning/deeplearning/mixed-precision.md)
- Architectures: [RNN-LSTM-GRU](MachineLearning/deeplearning/RNN-LSTM-GRU.md), [Transformers](MachineLearning/deeplearning/Transformers.md), [attention-is-all-you-need](MachineLearning/deeplearning/attention-is-all-you-need.md), [intro-to-convolutional-neural-networks](MachineLearning/deeplearning/intro-to-convolutional-neural-networks.md)
- Representations: [embeddings](MachineLearning/deeplearning/embeddings.md), [self-supervised-contrastive](MachineLearning/deeplearning/self-supervised-contrastive.md)
- Scaling/deployment: [distributed-training](MachineLearning/deeplearning/distributed-training.md), [optimizers](MachineLearning/deeplearning/optimizers.md), [model-optimization-for-inference](MachineLearning/deeplearning/model-optimization-for-inference.md), [how-to-use-lr-scheduler](MachineLearning/deeplearning/how-to-use-lr-scheduler.md), [hyperparameter-search](MachineLearning/deeplearning/hyperparameter-search.md)
- Generative: [GANs](MachineLearning/deeplearning/GANs.md), [diffusion-models](MachineLearning/deeplearning/diffusion-models.md)

### `MachineLearning/nlp` — natural language processing
- Foundation: [tokenization](MachineLearning/nlp/tokenization.md), [positional-encodings](MachineLearning/nlp/positional-encodings.md), [embeddings-for-search](MachineLearning/nlp/embeddings-for-search.md)
- Model families: [GPT-decoder-only-models](MachineLearning/nlp/GPT-decoder-only-models.md), [seq2seq-attention](MachineLearning/nlp/seq2seq-attention.md), [T5-encoder-decoder](MachineLearning/nlp/T5-encoder-decoder.md), [BERT](MachineLearning/nlp/BERT.md)
- Alignment/application: [instruction-tuning-rlhf](MachineLearning/nlp/instruction-tuning-rlhf.md), [prompting-in-context-learning](MachineLearning/nlp/prompting-in-context-learning.md), [RAG](MachineLearning/nlp/RAG.md), [agents-tool-calling](MachineLearning/nlp/agents-tool-calling.md), [nlp-tasks-fine-tuning](MachineLearning/nlp/nlp-tasks-fine-tuning.md)
- Evaluation: [GPT-evaluation](MachineLearning/nlp/GPT-evaluation.md)

### `MachineLearning/computer-vision` — CV
- Tasks: [object-detection](MachineLearning/computer-vision/object-detection.md), [segmentation](MachineLearning/computer-vision/segmentation.md), [pose-landmark-estimation](MachineLearning/computer-vision/pose-landmark-estimation.md), [hand-gesture-recognition](MachineLearning/computer-vision/hand-gesture-recognition.md)
- Practical: [data-augmentation](MachineLearning/computer-vision/data-augmentation.md), [real-time-processing](MachineLearning/computer-vision/real-time-processing.md), [report](MachineLearning/computer-vision/report.md), [report-completion](MachineLearning/computer-vision/report-completion.md), [hestreg](MachineLearning/computer-vision/hestreg.md), [hestreg-model](MachineLearning/computer-vision/hestreg-model.md)

### `MachineLearning/automl` — AutoML, tuning, meta-learning
- Tools: [optuna](MachineLearning/automl/hyperparameter-tuning/optuna.md), [autogluon](MachineLearning/automl/autogluon.md), [auto-sklearn](MachineLearning/automl/auto-sklearn.md), [h2o-automl](MachineLearning/automl/h2o-automl.md), [tpot](MachineLearning/automl/tpot.md), [mlbox](MachineLearning/automl/mlbox.md), [auto-keras](MachineLearning/automl/auto-keras.md), [azureml](MachineLearning/automl/azureml.md), [streamline](MachineLearning/automl/streamline.md), [auto-weka](MachineLearning/automl/auto-weka.md), [automated-feature-extraction](MachineLearning/automl/automated-feature-extraction.md)
- Search strategies: [bayesian-optimization](MachineLearning/automl/hyperparameter-tuning/bayesian-optimization.md), [random-search](MachineLearning/automl/hyperparameter-tuning/random-search.md), [grid-search](MachineLearning/automl/hyperparameter-tuning/grid-search.md), [gradient-based-optimization](MachineLearning/automl/hyperparameter-tuning/gradient-based-optimization.md), [Hyperband](MachineLearning/automl/hyperparameter-tuning/Hyperband.md), [asha-pbt-bohb](MachineLearning/automl/hyperparameter-tuning/asha-pbt-bohb.md), [tuning-methodology](MachineLearning/automl/hyperparameter-tuning/tuning-methodology.md)
- Architecture: [NAS](MachineLearning/automl/NAS.md)
- Meta/few-shot: [meta-learning](MachineLearning/automl/meta-learning.md), [metric-learning](MachineLearning/automl/metric-learning.md), [model-agnostic-meta-learning](MachineLearning/automl/model-agnostic-meta-learning.md), [few-shot-learning](MachineLearning/automl/few-shot-learning.md), [prototypical-matching-networks](MachineLearning/automl/prototypical-matching-networks.md), [reptile-meta-rl](MachineLearning/automl/reptile-meta-rl.md)
- Compare: [automl-comparison](MachineLearning/automl/automl-comparison.md), [hyperparameter-tuning](MachineLearning/automl/hyperparameter-tuning.md)

### `MachineLearning/evaluation` — metrics and evaluation
- [evaluation-metrics](MachineLearning/evaluation/evaluation-metrics.md) — the metric catalog, CV strategies, leakage pitfalls

### `MachineLearning/mlops` — putting models in production
- [mlops-overview](MachineLearning/mlops/mlops-overview.md) — tracking, registry, serving, drift, reproducibility

### `MachineLearning/reinforcement-learning` — RL
- [rl-overview](MachineLearning/reinforcement-learning/rl-overview.md) — MDPs, value functions, Q-learning, policy gradients

---

## PyTorch

### `PyTorch` — framework internals
- Tensor mechanics: [Tensor-TensorImpl-Storage](PyTorch/Tensor-TensorImpl-Storage.md), [Tensor-Subclass-Protocol](PyTorch/Tensor-Subclass-Protocol.md)
- Dispatch: [Dispatch-Key](PyTorch/Dispatch-Key.md), [Custom-Ops](PyTorch/Custom-Ops.md), [ATen-Codegen](PyTorch/ATen-Codegen.md)
- Autograd: [Autograd-Internals](PyTorch/Autograd-Internals.md)
- Performance/scale: [Performance](PyTorch/Performance.md), [Distributed-Collectives](PyTorch/Distributed-Collectives.md)
- Dev workflow: [Explore](PyTorch/Explore.md), [Build-From-Source](PyTorch/Build-From-Source.md), [Contributing](PyTorch/Contributing.md), [CI-Infra](PyTorch/CI-Infra.md)
- `Compilers/PyTorchCompiler/TorchInductor/` — [EntryPoint](Compilers/PyTorchCompiler/TorchInductor/EntryPoint.md) — `torch.compile()` → `compile_fx` call chain

---

## Programming

### `Programming/cpp` — C++
- [cpp-memory-model](Programming/cpp/cpp-memory-model.md), [cpp-memory-layout](Programming/cpp/cpp-memory-layout.md), [cpp-concurrency](Programming/cpp/cpp-concurrency.md), [cpp-smart-pointers](Programming/cpp/cpp-smart-pointers.md), [cpp-templates-stl](Programming/cpp/cpp-templates-stl.md), [cpp-debugging](Programming/cpp/cpp-debugging.md), [cpp-cmake](Programming/cpp/cpp-cmake.md)

### `Programming/python` — Python
- `core/` (internals): [frames-namespaces](Programming/python/core/frames-namespaces.md), [bytecode](Programming/python/core/bytecode.md), [data-model](Programming/python/core/data-model.md), [gil-threading](Programming/python/core/gil-threading.md), [python-memory](Programming/python/core/python-memory.md), [python-c-api](Programming/python/core/python-c-api.md), [python-performance](Programming/python/core/python-performance.md), [typechecking](Programming/python/core/typechecking.md), [type-system-depth](Programming/python/core/type-system-depth.md), [conditioning-on-a-python-subclass](Programming/python/core/conditioning-on-a-python-subclass.md)
- Ecosystem: [asyncio](Programming/python/asyncio.md), [packaging](Programming/python/packaging.md), [python-tooling](Programming/python/python-tooling.md)

### `Programming/shell` — shell and tooling
- [bash-scripting](Programming/shell/bash-scripting.md), [version-control](Programming/shell/version-control.md)

---

## OpenSource

- Working with big repos: [how-to-explore-large-repos](OpenSource/how-to-explore-large-repos.md)
- Contributing: [PR-Lifecycle](OpenSource/PR-Lifecycle.md), [writing-a-good-commit](OpenSource/writing-a-good-commit.md), [code-review-skills](OpenSource/code-review-skills.md), [good-issue-reports](OpenSource/good-issue-reports.md), [licenses-sustainability](OpenSource/licenses-sustainability.md)
- Debugging: [debugging-CI-failures](OpenSource/debugging-CI-failures.md)

---

## Roadmaps

- [where-am-i-now](Roadmaps/where-am-i-now.md) — the self-assessment map. **Start here.**
- [interview-prep](Roadmaps/interview-prep.md) — per-role interview prep
- ML roles: [ml-engineer](Roadmaps/ml-engineer.md), [mlops-engineer](Roadmaps/mlops-engineer.md), [llm-engineer](Roadmaps/llm-engineer.md), [data-analyst](Roadmaps/data-analyst.md), [research-scientist](Roadmaps/research-scientist.md), [ai-engineer](Roadmaps/ai-engineer.md)
- Infrastructure roles: [backend-infra-engineer](Roadmaps/backend-infra-engineer.md), [devops-engineer](Roadmaps/devops-engineer.md)
- Compiler/hardware roles: [ai-compiler-engineer](Roadmaps/ai-compiler-engineer.md), [ai-framework-engineer](Roadmaps/ai-framework-engineer.md), [triton-compiler-engineer](Roadmaps/triton-compiler-engineer.md), [gpu-architect](Roadmaps/gpu-architect.md), [hpc-engineer](Roadmaps/hpc-engineer.md), [rocm-developers](Roadmaps/rocm-developers.md)
- PyTorch-specific: [pytorch-developers](Roadmaps/pytorch-developers.md), [model-optimization-engineer](Roadmaps/model-optimization-engineer.md)

---

## Store

Reference material that spans the rest.

- [deep-learning-hardware](Store/deep-learning-hardware.md) — GPU/accelerator landscape
- [gpu-spec-catalog](Store/gpu-spec-catalog.md) — NVIDIA/AMD/Intel/cloud-chip specs
- [roofline-model](Store/roofline-model.md) — bandwidth vs compute reasoning
- [paper-library](Store/paper-library.md) — read / to-read papers
- [glossary](Store/glossary.md) — the acronym index
- [tooling](Store/tooling.md) — tools worth installing, by purpose
- [repos](Store/repos.md) — repos worth studying

---

## Tips for using this KB

- **Per-subtree `PLAN.md` roadmaps are all completed and removed** — the notes in each directory are the content.
- **Links are plain markdown** — `name → path/to/note.md` — so everything works on GitHub and any markdown viewer.
- **Start broad, then deep**: `Roadmaps/where-am-i-now.md` → `Compilers/DLCompilers/Comparison-Matrix.md` (or `MachineLearning/automl/automl-comparison.md`) → the individual notes.
- **`index.md`** is this file — the map of the whole KB; individual notes are the content.
