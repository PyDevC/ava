# MachineLearning/automl — Plan

## Goal of this directory

Cover **Automated Machine Learning** and the learning-to-learn family: the tools that search for pipelines/models automatically (auto-sklearn, H2O, TPOT...), the techniques behind them (Bayesian optimization, Hyperband — see [[hyperparameter-tuning]]), and meta-learning (few-shot, MAML, metric learning) which is the research side of "learning to learn".

## What's already here

- Tools: [[auto-sklearn]], [[auto-weka]], [[mlbox]], [[tpot]], [[h2o-automl]], [[azureml]], [[streamline]]
- Feature side: [[automated-feature-extraction]]
- Meta/few-shot: [[meta-learning]], [[few-shot-learning]], [[model-agnostic-meta-learning]], [[metric-learning]]
- hyperparameter-tuning/: grid, random, Bayesian, Hyperband, gradient-based (see its own [[hyperparameter-tuning/PLAN]])

## What to add next

- [ ] **NAS — Neural Architecture Search** (the automl-of-NNs topic, currently missing):
  - [ ] Reinforcement-learning-based search (the original approach)
  - [ ] Differentiable NAS (DARTS), weight sharing, one-shot
  - [ ] Zero-cost proxies / pruning-based search
  - [ ] Link to [[../deeplearning]] since it's about network design, not just HPs
- [ ] **Modern tools / benchmark studies**:
  - [ ] **AutoGluon** (Amazon) — the current strong open-source tabular AutoML; worth a note next to [[auto-sklearn]]
  - [ ] **Optuna** deep dive (the de-facto tuning library) — TPE sampler, pruning
  - [ ] A comparison note: when to reach for auto-sklearn vs H2O vs AutoGluon vs cloud ([[azureml]])
- [ ] **Search-strategy theory** (if going deeper):
  - [ ] SMAC (used by auto-sklearn) vs TPE (used by Optuna/MLBox) — how they differ
  - [ ] Multi-fidelity: ASHA, PBT (population-based training)
- [ ] **Meta-learning depth**:
  - [ ] Prototypical/matching networks (the metric-based [[few-shot-learning]] implementations)
  - [ ] Reptile (first-order MAML alternative), meta-learning for RL
  - [ ] In-context learning connection (transformers as meta-learners) — links to [[../nlp]]

## Prioritization

1. **AutoGluon + Optuna** — practical, immediately useful tools.
2. **NAS** — the big conceptual gap in "automl" as a term.
3. **Comparison note** — turns the tool notes into a decision guide.
