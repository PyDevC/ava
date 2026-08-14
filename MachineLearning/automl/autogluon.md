# AutoGluon

AutoGluon (Amazon) is the current strongest **open-source AutoML** — particularly for tabular data, where it routinely beats competitors (and often hand-tuned pipelines) by stacking ensembles of many model types (see [stacking](../algorithms/stacking.md)).

## The pitch

```python
from autogluon.tabular import TabularPredictor
predictor = TabularPredictor(label="target").fit(train_data)
```

That one call runs a **multi-layer stack ensemble**:

1. **Fit many base models** per family: GBDTs (LightGBM/XGBoost/CatBoost), RF/extra-trees, k-NN, neural nets (its own), linear models — each with its own hyperparameter search (Bayesian, see [Bayesian-optimization](hyperparameter-tuning/Bayesian-optimization.md)).
2. **Stack**: train a second-level model on the base models' predictions (stacking — [stacking](../algorithms/stacking.md)).
3. **Repeat / bag**: bagged (repeated CV) base models + a stacking level → the famous "AutoGluon just wins tabular" result comes mostly from this ensembling, *not* from brilliant hyperparameter search.

## Why it's different from classic AutoML

- **Smarter than "one pipeline"**: auto-sklearn/H2O search over *pipelines*; AutoGluon fits many model families and combines them — for tabular data, ensembling diverse families beats finding one perfect pipeline (see the comparison note in the [PLAN](PLAN.md)).
- **Multi-modal**: `autogluon.multimodal` handles image+text+tabular (uses deep learners); `autogluon.timeseries` for forecasting; `autogluon.vision`.
- **Predict & evaluate**: `predictor.leaderboard()`, `predictor.predict_proba()`, feature importance (`predictor.feature_importance()`), `predictor.save()` — production-grade plumbing.

## The trade-offs

- **Compute**: the full stack ensemble is *expensive* — many models × bagging × stacking. `fit(presets='medium_quality')` trades quality for speed; `presets='best_quality'` is the benchmark-setting (and slow) mode.
- **Latency**: a stacked ensemble = many models at inference. `predictor.distill()` trains a single student model to imitate the ensemble (see [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md)) for cheap serving.
- **Interpretability**: ensemble models are harder to explain than a single decision tree (see [model-interpretability](../algorithms/model-interpretability.md)).

## When to reach for it

- Tabular competitions / benchmarks where quality matters and you have compute.
- Baseline to beat: if AutoGluon's ensemble is hard to beat, spend effort elsewhere.
- When you need **time-boxed** AutoML (`fit(time_limit=...)`) on a dataset, not a hand-tuned pipeline.

## Related

- [stacking](../algorithms/stacking.md) — the core mechanism.
- [PLAN](PLAN.md) — vs auto-sklearn/H2O/cloud.
- [optuna](hyperparameter-tuning/optuna.md) — the tuning engine underneath.
- [model-interpretability](../algorithms/model-interpretability.md) — the cost of the ensemble.
