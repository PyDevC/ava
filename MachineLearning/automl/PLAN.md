# MachineLearning/automl — Plan

## Goal of this directory

Cover **Automated Machine Learning** and the learning-to-learn family: the tools that search for pipelines/models automatically (auto-sklearn, H2O, TPOT...), the techniques behind them (Bayesian optimization, Hyperband — see [[hyperparameter-tuning]]), and meta-learning (few-shot, MAML, metric learning) which is the research side of "learning to learn".

## What's already here

- Tools: [[auto-sklearn]], [[auto-weka]], [[mlbox]], [[tpot]], [[h2o-automl]], [[azureml]], [[streamline]], [[autogluon]], [[automl-comparison]]
- Feature side: [[automated-feature-extraction]]
- Meta/few-shot: [[meta-learning]], [[few-shot-learning]], [[model-agnostic-meta-learning]], [[metric-learning]], [[prototypical-matching-networks]], [[reptile-meta-rl]]
- Search strategy: [[NAS]]
- hyperparameter-tuning/: grid, random, Bayesian, Hyperband, gradient-based, Optuna, ASHA/PBT/BOHB, methodology (see its own [[hyperparameter-tuning/PLAN]])

## What to add next

- [x] **NAS — Neural Architecture Search** (the automl-of-NNs topic, currently missing):
  - [x] Reinforcement-learning-based search (the original approach)
  - [x] Differentiable NAS (DARTS), weight sharing, one-shot
  - [x] Zero-cost proxies / pruning-based search
  - [x] Link to [[../deeplearning]] since it's about network design, not just HPs
- [x] **Modern tools / benchmark studies**:
  - [x] **AutoGluon** (Amazon) — the current strong open-source tabular AutoML; worth a note next to [[auto-sklearn]]
  - [x] **Optuna** deep dive (the de-facto tuning library) — TPE sampler, pruning
  - [x] A comparison note: when to reach for auto-sklearn vs H2O vs AutoGluon vs cloud ([[azureml]])
- [x] **Search-strategy theory** (if going deeper):
  - [x] SMAC (used by auto-sklearn) vs TPE (used by Optuna/MLBox) — how they differ → see [[hyperparameter-tuning/Bayesian-optimization]] + [[hyperparameter-tuning/optuna]]
  - [x] Multi-fidelity: ASHA, PBT (population-based training) → see [[hyperparameter-tuning/asha-pbt-bohb]]
- [x] **Meta-learning depth**:
  - [x] Prototypical/matching networks (the metric-based [[few-shot-learning]] implementations)
  - [x] Reptile (first-order MAML alternative), meta-learning for RL
  - [x] In-context learning connection (transformers as meta-learners) — links to [[../nlp]] → see [[reptile-meta-rl]] + [[../nlp/prompting-in-context-learning]]

## Prioritization

1. **AutoGluon + Optuna** — practical, immediately useful tools.
2. **NAS** — the big conceptual gap in "automl" as a term.
3. **Comparison note** — turns the tool notes into a decision guide.
