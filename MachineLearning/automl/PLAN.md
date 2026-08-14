# MachineLearning/automl — Plan

## Goal of this directory

Cover **Automated Machine Learning** and the learning-to-learn family: the tools that search for pipelines/models automatically (auto-sklearn, H2O, TPOT...), the techniques behind them (Bayesian optimization, Hyperband — see [hyperparameter-tuning](hyperparameter-tuning.md)), and meta-learning (few-shot, MAML, metric learning) which is the research side of "learning to learn".

## What's already here

- Tools: [auto-sklearn](auto-sklearn.md), [auto-weka](auto-weka.md), [mlbox](mlbox.md), [tpot](tpot.md), [h2o-automl](h2o-automl.md), [azureml](azureml.md), [streamline](streamline.md), [autogluon](autogluon.md), [automl-comparison](automl-comparison.md)
- Feature side: [automated-feature-extraction](automated-feature-extraction.md)
- Meta/few-shot: [meta-learning](meta-learning.md), [few-shot-learning](few-shot-learning.md), [model-agnostic-meta-learning](model-agnostic-meta-learning.md), [metric-learning](metric-learning.md), [prototypical-matching-networks](prototypical-matching-networks.md), [reptile-meta-rl](reptile-meta-rl.md)
- Search strategy: [NAS](NAS.md)
- hyperparameter-tuning/: grid, random, Bayesian, Hyperband, gradient-based, Optuna, ASHA/PBT/BOHB, methodology (see its own [PLAN](hyperparameter-tuning/PLAN.md))

## What to add next

- [x] **NAS — Neural Architecture Search** (the automl-of-NNs topic, currently missing):
  - [x] Reinforcement-learning-based search (the original approach)
  - [x] Differentiable NAS (DARTS), weight sharing, one-shot
  - [x] Zero-cost proxies / pruning-based search
  - [x] Link to [deeplearning](../deeplearning/PLAN.md) since it's about network design, not just HPs
- [x] **Modern tools / benchmark studies**:
  - [x] **AutoGluon** (Amazon) — the current strong open-source tabular AutoML; worth a note next to [auto-sklearn](auto-sklearn.md)
  - [x] **Optuna** deep dive (the de-facto tuning library) — TPE sampler, pruning
  - [x] A comparison note: when to reach for auto-sklearn vs H2O vs AutoGluon vs cloud ([azureml](azureml.md))
- [x] **Search-strategy theory** (if going deeper):
  - [x] SMAC (used by auto-sklearn) vs TPE (used by Optuna/MLBox) — how they differ → see [Bayesian-optimization](hyperparameter-tuning/Bayesian-optimization.md) + [optuna](hyperparameter-tuning/optuna.md)
  - [x] Multi-fidelity: ASHA, PBT (population-based training) → see [asha-pbt-bohb](hyperparameter-tuning/asha-pbt-bohb.md)
- [x] **Meta-learning depth**:
  - [x] Prototypical/matching networks (the metric-based [few-shot-learning](few-shot-learning.md) implementations)
  - [x] Reptile (first-order MAML alternative), meta-learning for RL
  - [x] In-context learning connection (transformers as meta-learners) — links to [nlp](../nlp/PLAN.md) → see [reptile-meta-rl](reptile-meta-rl.md) + [prompting-in-context-learning](../nlp/prompting-in-context-learning.md)

## Prioritization

1. **AutoGluon + Optuna** — practical, immediately useful tools.
2. **NAS** — the big conceptual gap in "automl" as a term.
3. **Comparison note** — turns the tool notes into a decision guide.
