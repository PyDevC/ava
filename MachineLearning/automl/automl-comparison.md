# AutoML tools comparison

When do you reach for auto-sklearn vs H2O vs AutoGluon vs cloud AutoML? This is the decision guide for the tool notes in [PLAN](../) ([auto-sklearn](auto-sklearn.md), [h2o-automl](h2o-automl.md), [tpot](tpot.md), [autogluon](autogluon.md), [azureml](azureml.md)).

## The map

| Tool | Core technique | Tabular strength | Compute cost | Interpretability | Notes |
|---|---|---|---|---|---|
| **auto-sklearn** | pipeline search (BO/SMAC over sklearn pipelines) | very good | medium | medium | the classic; config-space covers preprocessors+estimators |
| **H2O AutoML** | leaderboard of tuned models (stacked ensemble) | very good | medium | high (inspect each model) | Java/H2O stack; strong for tabular, R/Java-friendly |
| **AutoGluon** | multi-model stack ensemble + bagging | **best-in-class** | **high** | low (ensembles) | the modern default for quality; see [autogluon](autogluon.md) |
| **TPOT** | genetic programming over sklearn pipelines | good | medium-high | low | code-generating; unusual search (see [tpot](tpot.md)) |
| **Azure AutoML** (cloud) | managed pipelines + deep learners | good | n/a (pay) | medium | the "no infra" option (see [azureml](azureml.md)) |

## The decision tree

1. **Tabular, quality matters, you have compute** → **AutoGluon** (`presets='best_quality'`). It wins on benchmark after benchmark *because* of the stack ensemble, not tuning cleverness.
2. **Tabular, need to inspect/ship each model** (regulatory, R/Java) → **H2O** (leaderboard + MOJO export) or **auto-sklearn** (sklearn-native).
3. **Need a quick, explainable, code-free-ish baseline** → **auto-sklearn** or **TPOT** on a downsampled dataset.
4. **No local compute / need managed tracking & deployment** → cloud (Azure AutoML / SageMaker AutoPilot / GCP Vertex).
5. **Not tabular** (NLP/CV) → these tools underperform; use Optuna + your own deep-learning stack ([optuna](hyperparameter-tuning/optuna.md), [model-optimization](../deeplearning/model-optimization.md)).

## The universal caveats

- **"AutoML won" almost always means the *ensemble/stacking* won**, not the search algorithm. If you need one deployable model, **distill** the ensemble (AutoGluon `.distill()`, see [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md)).
- AutoML results are only as good as your eval protocol: same CV fold discipline as any manual tuning (see [cross-validation](../algorithms/cross-validation.md)).
- **Time-limit everything**: `time_limit`/`max_time` are the right way to think of cost; the tools' docs all use it.

## Related

- [autogluon](autogluon.md) — the modern standout.
- [hyperparameter-tuning](hyperparameter-tuning/) — the algorithms these tools run.
- [stacking](../algorithms/stacking.md) — the mechanism behind the wins.
- [azureml](azureml.md) — the cloud option.
