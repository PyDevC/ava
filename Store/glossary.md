# Glossary / acronym index

The acronym soup of the ML-compiler world, indexed into the real notes. When a note mentions an acronym you half-remember, look here.

## ML / training

- **DDP / FSDP** — DistributedDataParallel / Fully Sharded Data Parallel → [distributed-training](../MachineLearning/deeplearning/distributed-training.md)
- **GQA** — Grouped-Query Attention → [GPT-decoder-only-models](../MachineLearning/nlp/GPT-decoder-only-models.md)
- **KV cache** — the cached attention keys/values during generation → [GPT-decoder-only-models](../MachineLearning/nlp/GPT-decoder-only-models.md)
- **LoRA** — Low-Rank Adaptation → [nlp-tasks-fine-tuning](../MachineLearning/nlp/nlp-tasks-fine-tuning.md)
- **RLHF / DPO / SFT** — Reinforcement Learning from Human Feedback / Direct Preference Optimization / Supervised Fine-Tuning → [instruction-tuning-rlhf](../MachineLearning/nlp/instruction-tuning-rlhf.md)
- **RAG** — Retrieval-Augmented Generation → [RAG](../MachineLearning/nlp/RAG.md)
- **TTA** — Test-Time Augmentation → [data-augmentation](../MachineLearning/computer-vision/data-augmentation.md)
- **NAS** — Neural Architecture Search → [NAS](../MachineLearning/automl/NAS.md)
- **PTQ / QAT** — Post-Training Quantization / Quantization-Aware Training → [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md)

## Compilers / frameworks

- **IR** — Intermediate Representation → [PLAN](../Compilers/IR/)
- **SSA** — Static Single Assignment → [Dominators](../Compilers/IR/SSA/Dominators.md)
- **HLO / StableHLO** — (Stable) High-Level Optimizer IR → [XLA-StableHLO](../Compilers/DLCompilers/XLA-StableHLO.md), [tosa-stablehlo](../Compilers/MLIR/Dialects/tosa-stablehlo.md)
- **MLIR / ODS / DRR** — Multi-Level IR / Operation Definition Spec / Declarative Rewrite Rules → [PLAN](../Compilers/MLIR/)
- **HAL** — Hardware Abstraction Layer (IREE's runtime dispatch layer) → [IREE](../Compilers/DLCompilers/IREE/)
- **FX** — the torch.fx graph IR → [FX-Graph-IR](../Compilers/PyTorchCompiler/FX-Graph-IR.md)
- **EP** — Execution Provider (ONNX Runtime) → [ONNXRuntime](../Compilers/DLCompilers/ONNXRuntime.md)
- **DCE / CSE** — Dead Code Elimination / Common Subexpression Elimination → [PLAN](../Compilers/IR/)

## Hardware / systems

- **CUDA / HIP** — NVIDIA / AMD GPU programming models → [rocm-developers](../Roadmaps/rocm-developers.md)
- **RCCL / NCCL** — AMD / NVIDIA collective-communication libraries → [Distributed-Collectives](../PyTorch/Distributed-Collectives.md)
- **HBM / GDDR** — high-bandwidth memory / graphics DDR → [gpu-spec-catalog](gpu-spec-catalog.md)
- **GIL** — Global Interpreter Lock → [gil-threading](../Programming/python/core/gil-threading.md)
- **ASAN / TSAN / UBSAN** — address / thread / undefined-behavior sanitizers → [cpp-debugging](../Programming/cpp/cpp-debugging.md)
- **CRTP** — Curiously Recurring Template Pattern → [cpp-templates-stl](../Programming/cpp/cpp-templates-stl.md)

## The index-of-indexes

- Each directory's own `README.md`/notes serve as the "what's here"; the per-subtree `PLAN.md` roadmaps were completed and removed.
- [where-am-i-now](../Roadmaps/where-am-i-now.md) — which areas the repo covers at all.

## Related

- This index is meant to *link*, not explain — the notes it points to do the explaining.
- [PLAN](./) — the home directory for reference material.
