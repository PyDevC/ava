# MachineLearning — Plan

## Goal of this directory

Be the **breadth** half of my knowledge base: the core ML concepts, algorithms, and techniques that any ML engineer must know, organized by sub-topic. It's intentionally library-agnostic (concepts first), with PyTorch-specific notes living in [PLAN](../PyTorch/PLAN.md).

## What's already here

- **algorithms/** — core ML algorithms + building blocks: boosting (GB/XGB/LightGBM), trees, classification, regression (tabular/weighted), loss/cost functions, NN basics, transfer learning, ranking, SOM.
- **automl/** — AutoML tools + meta-learning: auto-sklearn, H2O, TPOT, MLBox, AzureML, few-shot/meta/MAML/metric-learning, plus hyperparameter-tuning/.
- **computer-vision/** — the hestreg hand-gesture project: system, model, pipeline, report.
- **deeplearning/** — deep learning notes: Transformers, attention paper, CNN intro, hyperparameter search, LR schedulers, optimizers.
- **mathematical-function/** — the math building blocks: affine/linear/logistic/logit/softmax/quantile/indicator/Lipschitz.
- **nlp/** — NLP: BERT (only one note so far — thinnest area).

## What to add next

- [x] **Algorithms** — fill the classics that are missing:
  - [x] k-NN, Naive Bayes, SVM, logistic regression (explicit note), k-means/PCA/clustering, bias-variance tradeoff, regularization
- [x] **Deep learning** — the theory gaps:
  - [x] Backpropagation/autograd deep dive, embeddings, batch norm / layer norm, overfitting & regularization (dropout, weight decay)
  - [x] RNN/LSTM/GRU (the pre-transformer sequence models), seq2seq
  - [x] GANs, diffusion models, self-supervised learning (self-supervised still open — see [deeplearning](deeplearning/PLAN.md))
  - [x] Distributed training (DDP/FSDP), mixed precision
- [x] **NLP** — biggest gap; needs: tokenization/embeddings, attention in depth, GPT/decoder story, RAG/agents, eval metrics (BLEU/ROUGE/perplexity)
- [x] **mathematical-function** — add the activation function family (ReLU/GELU/swish — GELU/swish still open), distributions (normal/Bernoulli/Categorical), norms (L1/L2), and gradient/Hessian.
- [x] **automl** — add NAS (neural architecture search), Optuna deep-dive, AutoGluon, SMAC; contrast tools.
- [ ] **Cross-cutting (new subdirs?)**:
  - [ ] evaluation/ — metrics catalog, CV strategies, leakage pitfalls
  - [ ] mlops/ — or fold into Roadmaps; experiment tracking, serving, drift
  - [ ] reinforcement-learning/ — a whole missing field (Q-learning, policy gradients) — decide later if needed

## Prioritization

1. **NLP** — currently the thinnest and most relevant to modern ML work.
2. **Algorithms classics** (logistic regression, k-means, SVM) — foundational, cheap to write.
3. **Backprop/regularization** — the DL theory every engineer should nail.
