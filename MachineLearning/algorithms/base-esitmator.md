# Base estimator

The base estimator (in sklearn: `base_estimator`, newer versions renamed `estimator`) is the **weak learner** that a boosting/bagging ensemble builds on top of.

In `GradientBoostingClassifier` / `GradientBoostingRegressor` the default is a shallow `DecisionTreeRegressor` (max depth 3 by default). Each boosting round fits a fresh copy of this base estimator to the current residuals, so the "model" is really the sum of many small base estimators.

## What makes a good base estimator for boosting

- **Weak but slightly better than random**: if it's too strong it overfits the residuals; too weak and you need thousands of rounds.
- **Stable / low variance** at small size — trees with small depth are the classic choice.
- For [[Gradient-boosting]], [[XGBoost]], [[LightGBM]] the base learner is always a regression tree, regardless of the final task (classification is handled by the loss function, e.g. logistic).

## Other uses of the term

- In **bagging** (random forests), the base estimator is a full (deep) decision tree and diversity comes from data sampling.
- In **stacking**, the "base estimators" are the diverse level-0 models whose predictions feed the meta-model.

So: base estimator = the building block. Changing it changes the whole ensemble family.
