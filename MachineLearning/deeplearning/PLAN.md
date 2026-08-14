# MachineLearning/deeplearning — Plan

## Goal of this directory

Cover the **deep learning theory and practice** that underlies modern ML: architectures (CNNs, Transformers, RNNs), training mechanics (optimizers, schedulers, regularization), and the pipeline (hyperparameter search, model optimization). Notes should be *training-practical* — things I actually use when training models.

## What's already here

- Architectures: [[Transformers]], [[attention-is-all-you-need]] (paper notes), [[An-introduction-to-convolutional-neural-networks]]
- Training mechanics: [[model-optimization]] (optimizers), [[how-to-use-lr-scheduler]], [[hyperparameter-search]]

## What to add next

- [ ] **Backprop & autograd** — the missing foundation:
  - [ ] Chain rule in layers, how gradients flow, vanishing/exploding gradients
  - [ ] How PyTorch autograd builds its graph (ties to [[../../PyTorch/PLAN]])
- [ ] **Regularization & normalization** (extend [[model-optimization]]):
  - [ ] Dropout, weight decay, data augmentation, early stopping
  - [ ] BatchNorm / LayerNorm (already referenced in attention notes) — what they actually fix
- [ ] **More architectures**:
  - [ ] RNN/LSTM/GRU — the pre-transformer sequence models (missing entirely)
  - [ ] Seq2seq + attention-based translation (bridge between RNN and Transformer notes)
  - [ ] Embeddings (word/learned embeddings — see [[../nlp]] link)
- [ ] **Modern generative models** (decide scope):
  - [ ] GANs (concept + training instability)
  - [ ] Diffusion models (DDPM; the modern image generation standard)
  - [ ] Self-supervised / contrastive learning (extends [[../automl/metric-learning]])
- [ ] **Training at scale**:
  - [ ] Distributed training: DDP, FSDP, gradient accumulation
  - [ ] Mixed precision (fp16/bf16), memory-efficient training — ties to [[model-optimization]]
- [ ] **Model optimization for inference** (deployment side):
  - [ ] Quantization, pruning, distillation — extends [[model-optimization]]; see [[../../Compilers/DLCompilers/TorchInductor]] for the compiler view

## Prioritization

1. **Backprop** then **regularization/normalization** — the foundations everything else assumes.
2. **RNN/LSTM + embeddings** — classic sequence modeling, needed before going deeper into NLP.
3. **Distributed + mixed precision** — necessary as models grow.
