# MLBox

MLBox is an open-source AutoML Python library focused on **tabular data**, with a simple "load → optimize → build model → predict" workflow. It's a lighter-weight alternative to [[auto-sklearn]] / [[h2o-automl]].

## The pipeline it automates

1. **Data preparation** — reading, cleaning, missing-value handling, outlier detection, normalization, feature generation (it has some automated feature engineering primitives — see [[automated-feature-extraction]]).
2. **Hyperparameter optimization** — uses **Hyperopt** (TPE — Tree-structured Parzen Estimator, a form of [[hyperparameter-tuning/Bayesian-optimization]]) over a set of models: LightGBM/XGBoost, random forests, extra trees, etc.
3. **Prediction** — trains the best config, predicts, and can export.

## What stands out

- **Very easy API** (a few function calls) and fast to prototype.
- Good automatic handling of missing values and outliers out of the box.
- Realistic auto-feature engineering for tabular data.

## Practical notes

- Best for quick tabular baselines / small-to-medium datasets.
- Less mature/actively maintained than sklearn, TPOT, or H2O — fine for experiments, be careful for serious production.
- The model space is mostly tree models, so it inherits their strength on structured data (see [[MachineLearning/algorithms/tabular-regression]]).
