---
id: auto-sklearn
aliases: []
tags: []
---

# Auto-sklearn

Auto-sklearn is an AutoML framework that wraps the sklearn ecosystem: it searches over **algorithm + hyperparameter + preprocessing pipeline** combinations for a given dataset, so you get a ready-to-use sklearn pipeline.

## How it works

1. **Meta-learning (warm-start)**: given a new dataset, it looks at 100+ previously seen datasets and their winning pipelines, and uses those as a *prior* for the search. This is why it starts fast.
2. **Bayesian optimization** over the pipeline space (using SMAC — Sequential Model-based Algorithm Configuration): fits a surrogate model of pipeline performance, picks promising configs, evaluates them.
3. **Ensemble construction**: instead of trusting one best pipeline, it builds an ensemble of the best-found pipelines (weighted voting / stacking), which typically beats the single best config.

## Key ingredients

- **Config space**: preprocessors (scalers, imputers, encoders) × classifiers/regressors (trees, boosting, SVMs, linear models, k-NN, MLP...) × their hyperparameters.
- **Censored budget**: poor configurations are killed early (successive halving style) to save compute.
- Handles missing values, categoricals, scaling automatically.

## Practical notes

- Great default for tabular AutoML (see [[MachineLearning/algorithms/tabular-regression]]).
- Slow on huge datasets (each evaluation trains a full model) — set a time budget (`time_left_for_this_task`).
- Related: [[mlbox]], [[tpot]], [[h2o-automl]] are the main competitors.
