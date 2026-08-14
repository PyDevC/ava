---
id: tpot
aliases: []
tags: []
---

# TPOT (Tree-based pipeline optimization tool)

TPOT uses **genetic programming** to evolve an end-to-end sklearn-style pipeline: a sequence of feature preprocessors + a classifier, with the goal of maximizing classification accuracy.

## The search

- Evolves populations of pipelines ("generations"); each generation mutates/crosses over pipeline components and keeps the fittest.
- Each candidate is evaluated by fitting it and scoring on a validation split — so the search is expensive, but fully automated.

## Operator types

- **Classification operators**: the ML classifiers; some store a classifier's predictions as a new feature (stacking-style, see [stacking](../algorithms/stacking.md)).
- **Feature preprocessing operators**: scalers and normalizers for data cleansing.
- **Feature selection operators**: reduce feature count — VarianceThreshold, SelectKBest, SelectPercentile, SelectFwe, Recursive Feature Elimination (RFE).

The evolutionary angle is what makes it "optimization": it samples and mutates pipelines, each generation getting closer to a good algorithm for your data (vs [grid-search](hyperparameter-tuning/grid-search.md), which explores a fixed grid).

## Related

- [automl-comparison](automl-comparison.md) — where TPOT sits vs the other AutoML tools.
- [stacking](../algorithms/stacking.md) — the "predictions as features" trick.
