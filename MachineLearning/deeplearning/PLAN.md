# MachineLearning/deeplearning — Plan

## Goal of this directory

Cover the **deep learning theory and practice** that underlies modern ML: architectures (CNNs, Transformers, RNNs), training mechanics (optimizers, schedulers, regularization), and the pipeline (hyperparameter search, model optimization). Notes should be *training-practical* — things I actually use when training models.

## What's already here

- Architectures: [[Transformers]], [[attention-is-all-you-need]] (paper notes), [[An-introduction-to-convolutional-neural-networks]], [[RNN-LSTM-GRU]]
- Training mechanics: [[model-optimization]] (optimizers), [[how-to-use-lr-scheduler]], [[hyperparameter-search]], [[backpropagation]], [[regularization-normalization]], [[embeddings]], [[mixed-precision]], [[distributed-training]]
- Generative: [[GANs]], [[diffusion-models]]
- Inference: [[model-optimization-for-inference]]

## What to add next

- [x] **Backprop & autograd** — the missing foundation:
  - [x] Chain rule in layers, how gradients flow, vanishing/exploding gradients
  - [x] How PyTorch autograd builds its graph (ties to [[../../PyTorch/PLAN]])
- [x] **Regularization & normalization** (extend [[model-optimization]]):
  - [x] Dropout, weight decay, data augmentation, early stopping
  - [x] BatchNorm / LayerNorm (already referenced in attention notes) — what they actually fix
- [x] **More architectures**:
  - [x] RNN/LSTM/GRU — the pre-transformer sequence models (missing entirely)
  - [ ] Seq2seq + attention-based translation (bridge between RNN and Transformer notes) → folded into [[../nlp]] plan
  - [x] Embeddings (word/learned embeddings — see [[../nlp]] link)
- [x] **Modern generative models** (decide scope):
  - [x] GANs (concept + training instability)
  - [x] Diffusion models (DDPM; the modern image generation standard)
  - [ ] Self-supervised / contrastive learning (extends [[../automl/metric-learning]])
- [x] **Training at scale**:
  - [x] Distributed training: DDP, FSDP, gradient accumulation
  - [x] Mixed precision (fp16/bf16), memory-efficient training — ties to [[model-optimization]]
- [x] **Model optimization for inference** (deployment side):
  - [x] Quantization, pruning, distillation — extends [[model-optimization]]; see [[../../Compilers/DLCompilers/TorchInductor]] for the compiler view

## Prioritization

1. **Backprop** then **regularization/normalization** — the foundations everything else assumes.
2. **RNN/LSTM + embeddings** — classic sequence modeling, needed before going deeper into NLP.
3. **Distributed + mixed precision** — necessary as models grow.
