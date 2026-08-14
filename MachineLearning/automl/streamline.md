# Streamline

Streamline is a lightweight open-source AutoML framework for **tabular data**, made to fit into existing Python workflows (it's built as a thin wrapper that adds AutoML on top of your own code). It's a smaller, friendlier cousin of [[auto-sklearn]] and [[mlbox]].

## What it does

- Runs a curated set of sklearn-compatible models (linear, trees, boosting, etc.) with cross-validation.
- Does **Bayesian hyperparameter optimization** (Optuna-based) on the promising ones.
- Produces a ranking of models with metrics + feature importance, and can save/export the best model.

## Why it's nice

- Minimal setup and clean API; the README shows you can go from raw dataset to a tuned model ranking in a few lines.
- Transparency: it's simple enough to read the source and understand exactly what it did — good for learning AutoML internals.
- Good for benchmarks and quick baselines.

## Practical notes

- Less powerful/robust than H2O or auto-sklearn on gnarly data, but far easier to reason about and debug.
- Uses Optuna (a modern [[hyperparameter-tuning/Bayesian-optimization]] implementation) under the hood.

## Related

- [[h2o-automl]], [[auto-sklearn]], [[tpot]], [[mlbox]] — the AutoML landscape it belongs to.
- [[MachineLearning/algorithms/tabular-regression]] — the task it targets.
