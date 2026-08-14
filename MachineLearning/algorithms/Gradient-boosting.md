---
id: Gradient-boosting
aliases: []
tags: []
---

Gradient boosting builds a strong model as an *additive ensemble* of weak learners (typically shallow decision trees), where each new tree is trained to fit the **negative gradient of the loss** w.r.t. the current prediction. This is why it's "gradient" boosting — it's essentially functional gradient descent over the space of predictors.

## Intuition

- Start with a constant model `F_0` that minimizes the loss (e.g. the mean for squared error).
- For each round `m`:
  1. Compute pseudo-residuals `r_i = -∂L(y_i, F(x_i)) / ∂F(x_i)` — this is the "gradient" direction each sample wants to move.
  2. Fit a small tree `h_m` to those residuals.
  3. Update `F = F + η·h_m` where `η` is the learning rate (shrinkage).

Each tree fixes the mistakes the previous ones left behind, and small `η` + many trees generalizes better than few big trees.

## Why it works

Gradient descent but in *function space*: instead of updating a parameter vector, we update the function itself by adding a step in the direction of the steepest decrease of the loss. The trees are restricted to a hypothesis space (small depth), so it's a constrained functional gradient step.

## Notes

- Works for regression, classification, ranking — any differentiable loss.
- This family includes [XGBoost](XGBoost.md), [LightGBM](LightGBM.md), and [decision trees](descision-trees.md) as base learners.
- Key hyperparameters: number of trees (`n_estimators`), learning rate, max depth, subsampling.
- The base estimator (`base_estimator` / `estimator`) is normally a decision tree — see [base-esitmator](base-esitmator.md).
