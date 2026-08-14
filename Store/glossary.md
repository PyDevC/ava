# Glossary / acronym index

The acronym soup of the ML-compiler world, indexed into the real notes. When a note mentions an acronym you half-remember, look here.

## ML / training

- **DDP / FSDP** — DistributedDataParallel / Fully Sharded Data Parallel → [[MachineLearning/deeplearning/distributed-training]]
- **GQA** — Grouped-Query Attention → [[MachineLearning/nlp/GPT-decoder-only-models]]
- **KV cache** — the cached attention keys/values during generation → [[MachineLearning/nlp/GPT-decoder-only-models]]
- **LoRA** — Low-Rank Adaptation → [[MachineLearning/nlp/nlp-tasks-fine-tuning]]
- **RLHF / DPO / SFT** — Reinforcement Learning from Human Feedback / Direct Preference Optimization / Supervised Fine-Tuning → [[MachineLearning/nlp/instruction-tuning-rlhf]]
- **RAG** — Retrieval-Augmented Generation → [[MachineLearning/nlp/RAG]]
- **TTA** — Test-Time Augmentation → [[MachineLearning/computer-vision/data-augmentation]]
- **NAS** — Neural Architecture Search → [[MachineLearning/automl/NAS]]
- **PTQ / QAT** — Post-Training Quantization / Quantization-Aware Training → [[MachineLearning/deeplearning/model-optimization-for-inference]]

## Compilers / frameworks

- **IR** — Intermediate Representation → [[Compilers/IR/PLAN]]
- **SSA** — Static Single Assignment → [[Compilers/IR/SSA/Dominators]]
- **HLO / StableHLO** — (Stable) High-Level Optimizer IR → [[Compilers/DLCompilers/XLA-StableHLO]], [[Compilers/MLIR/Dialects/tosa-stablehlo]]
- **MLIR / ODS / DRR** — Multi-Level IR / Operation Definition Spec / Declarative Rewrite Rules → [[Compilers/MLIR/PLAN]]
- **HAL** — Hardware Abstraction Layer (IREE's runtime dispatch layer) → [[Compilers/DLCompilers/IREE]]
- **FX** — the torch.fx graph IR → [[Compilers/PyTorchCompiler/FX-Graph-IR]]
- **EP** — Execution Provider (ONNX Runtime) → [[Compilers/DLCompilers/ONNXRuntime]]
- **DCE / CSE** — Dead Code Elimination / Common Subexpression Elimination → [[Compilers/IR/PLAN]]

## Hardware / systems

- **CUDA / HIP** — NVIDIA / AMD GPU programming models → [[Roadmaps/rocm-developers]]
- **RCCL / NCCL** — AMD / NVIDIA collective-communication libraries → [[PyTorch/Distributed-Collectives]]
- **HBM / GDDR** — high-bandwidth memory / graphics DDR → [[Store/gpu-spec-catalog]]
- **GIL** — Global Interpreter Lock → [[Programming/python/core/gil-threading]]
- **ASAN / TSAN / UBSAN** — address / thread / undefined-behavior sanitizers → [[Programming/cpp/cpp-debugging]]
- **CRTP** — Curiously Recurring Template Pattern → [[Programming/cpp/cpp-templates-stl]]

## The index-of-indexes

- Every `PLAN.md` file at a directory root is the roadmap for that subtree — the "where am I and what's next" of each topic.
- [[Roadmaps/where-am-i-now]] — which areas the repo covers at all.

## Related

- This index is meant to *link*, not explain — the notes it points to do the explaining.
- [[Store/PLAN]] — the home directory for reference material.
