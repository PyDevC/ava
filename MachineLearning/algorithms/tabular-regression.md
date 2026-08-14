# Tabular regression

Tabular regression = predicting a **continuous target** from rows of structured (spreadsheet-like) features. This is the classic supervised setting, and the algorithms you actually reach for in practice are mostly tree-based.

## The go-to toolset (my order of attack)

1. **[XGBoost](XGBoost.md) / [LightGBM](LightGBM.md)** — best-in-class defaults for tabular data; fast, robust, handle missing values + categoricals.
2. **[Gradient-boosting](Gradient-boosting.md)** — the family they belong to; understand it first.
3. Random forest / ExtraTrees — good baseline, no learning rate to tune.
4. Linear regression / ridge — cheap sanity check, interpretable.
5. Neural nets (MLP) — last; tabular NNs need careful feature scaling and often lose to trees.

## Pipeline essentials

- **Preprocessing**: handle missing values (tree models tolerate them, linear models don't), encode categoricals (ordinal/one-hot/target-encoding), scale features for linear models only.
- **Cross-validation**: use `KFold`/`StratifiedKFold` (stratify a binned target if continuous) — never evaluate on the training data.
- **Feature engineering**: interactions, aggregates, domain features usually beat model choice.
- **Evaluation**: RMSE / MAE / R² depending on the business cost (see [cost-function](cost-function.md)). Consider MAPE for relative errors.

## When trees vs linear

- Linear: interpretability, high-cardinality data, near-linear relationships, small data.
- Trees/boosting: non-linearities, interactions, messy features, large data.

## Related

- [weighted-linear-regression](weighted-linear-regression.md) — when samples have unequal importance.
- [loss-function](loss-function.md) — pick RMSE vs MAE by outlier tolerance.
- [descision-trees](descision-trees.md) — the base learner underneath everything tree-based.
