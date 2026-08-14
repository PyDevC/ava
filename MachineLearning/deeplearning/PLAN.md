# MachineLearning/deeplearning — Plan

## Goal of this directory

Cover the **deep learning theory and practice** that underlies modern ML: architectures (CNNs, Transformers, RNNs), training mechanics (optimizers, schedulers, regularization), and the pipeline (hyperparameter search, model optimization). Notes should be *training-practical* — things I actually use when training models.

## What's already here

- Architectures: [Transformers](Transformers.md), [attention-is-all-you-need](attention-is-all-you-need.md) (paper notes), [An-introduction-to-convolutional-neural-networks](An-introduction-to-convolutional-neural-networks.md), [RNN-LSTM-GRU](RNN-LSTM-GRU.md)
- Training mechanics: [model-optimization](model-optimization.md) (optimizers), [how-to-use-lr-scheduler](how-to-use-lr-scheduler.md), [hyperparameter-search](hyperparameter-search.md), [backpropagation](backpropagation.md), [regularization-normalization](regularization-normalization.md), [embeddings](embeddings.md), [mixed-precision](mixed-precision.md), [distributed-training](distributed-training.md)
- Generative: [GANs](GANs.md), [diffusion-models](diffusion-models.md)
- Inference: [model-optimization-for-inference](model-optimization-for-inference.md)

## What to add next

- [x] **Backprop & autograd** — the missing foundation:
  - [x] Chain rule in layers, how gradients flow, vanishing/exploding gradients
  - [x] How PyTorch autograd builds its graph (ties to [PLAN](../../PyTorch/PLAN.md))
- [x] **Regularization & normalization** (extend [model-optimization](model-optimization.md)):
  - [x] Dropout, weight decay, data augmentation, early stopping
  - [x] BatchNorm / LayerNorm (already referenced in attention notes) — what they actually fix
- [x] **More architectures**:
  - [x] RNN/LSTM/GRU — the pre-transformer sequence models (missing entirely)
  - [x] Seq2seq + attention-based translation (bridge between RNN and Transformer notes) → folded into [nlp](../nlp/) plan
  - [x] Embeddings (word/learned embeddings — see [nlp](../nlp/) link)
- [x] **Modern generative models** (decide scope):
  - [x] GANs (concept + training instability)
  - [x] Diffusion models (DDPM; the modern image generation standard)
  - [ ] Self-supervised / contrastive learning (extends [metric-learning](../automl/metric-learning.md))
- [x] **Training at scale**:
  - [x] Distributed training: DDP, FSDP, gradient accumulation
  - [x] Mixed precision (fp16/bf16), memory-efficient training — ties to [model-optimization](model-optimization.md)
- [x] **Model optimization for inference** (deployment side):
  - [x] Quantization, pruning, distillation — extends [model-optimization](model-optimization.md); see [TorchInductor](../../Compilers/DLCompilers/TorchInductor/PLAN.md) for the compiler view

## Prioritization

1. **Backprop** then **regularization/normalization** — the foundations everything else assumes.
2. **RNN/LSTM + embeddings** — classic sequence modeling, needed before going deeper into NLP.
3. **Distributed + mixed precision** — necessary as models grow.
