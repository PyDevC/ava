---
id: h2o-automl
aliases: []
tags: []
---

# H2O AutoML

H2O runs a leaderboard for ranking models all of which can be easily exported for use in production.

H2O AutoML is the in-memory, distributed AutoML from the H2O platform. You give it a dataset + target, it trains many models, ranks them on a leaderboard, and lets you export/deploy the winner (or an ensemble).

## How it works

1. Runs a fixed, sensible set of base models: GLM, GBM (gradient boosting), XGBoost, LightGBM (in newer versions), Deep Learning (MLP), and a couple of tuned variants of each.
2. Does hyperparameter search on the promising families (grid / random search with early stopping).
3. Builds **stacked ensembles** (Stacked Ensembles / Super Learner) of the best base models — usually the top of the leaderboard.
4. Uses cross-validation internally (optionally time-series CV), and reports performance honestly per fold.

## Why people like it

- Very fast and scalable (Java-based H2O engine, in-memory, handles big tables).
- Good default leaderboard with explainability (SHAP, partial dependence, auto-report).
- Production story: export the model as a POJO/MOJO (Java object) that can be dropped into a serving app with no Python.

## Practical notes

- The search is *not* full Bayesian AutoML like [auto-sklearn](auto-sklearn.md); it's a smart heuristic over a curated model zoo. Results are excellent but the "search" is less principled.
- Good tabular baseline vs [auto-sklearn](auto-sklearn.md), [mlbox](mlbox.md), [tpot](tpot.md), and [azureml](azureml.md) AutoML.

## Related

- [Gradient-boosting](../algorithms/Gradient-boosting.md) — GBM is usually the workhorse under the hood.
- [ranking](../algorithms/ranking.md) — leaderboard = ranking models by a chosen metric.
