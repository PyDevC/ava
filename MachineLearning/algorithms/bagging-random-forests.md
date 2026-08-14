# Bagging and random forests

Bagging (Bootstrap AGGregatING) reduces **variance** by training many models on different bootstrap samples of the data and averaging their predictions (Breiman, 1996). Random forests are bagging applied to decision trees.

## Bagging

1. Sample `n` bootstrap sets (resample the training data with replacement).
2. Train one model per set.
3. Average (regression) or majority-vote (classification) the predictions.

Averaging independent-ish models cuts variance by roughly `1/m` (uncorrelated case). Each model is trained on ~63% of the data (the rest are "out-of-bag" samples) — the OOB error is a free validation estimate.

## Why it works

Models trained on slightly different data make *different* mistakes; averaging cancels the noise (variance ↓) while keeping the same bias (see [bias-variance-tradeoff](bias-variance-tradeoff.md)). The trees must be **decorrelated** for this to work — which is where forests go further than plain bagging.

## Random forests: the extra trick

Beyond bagging, each split considers only a random subset of `m` features (typically `m ≈ √d`). This forces trees to differ structurally — otherwise every tree would pick the same strong features and stay correlated. Result: lower variance without much bias increase.

Key facts:

- **No cross-validation needed** — OOB error estimates test error well.
- Handles high-dim, non-linear, and mixed data with no scaling (see [tabular-regression](tabular-regression.md)).
- `n_estimators` (more is better, diminishing returns), `max_depth`/`min_samples_leaf` control variance further.
- Feature importance = how much splitting on a feature reduces impurity (see the interpretability note in [PLAN](../PLAN.md)).

## Bagging vs boosting

- **Bagging**: parallel, variance-reducing, deep trees (each individual model is allowed to overfit; the average fixes it).
- **Boosting**: sequential, bias-reducing, shallow trees that fix the previous trees' mistakes (see [Gradient-boosting](Gradient-boosting.md), [XGBoost](XGBoost.md)).

## Stacking

Where bagging averages *same-type* models, **stacking** trains *diverse* models and lets a meta-model learn how to combine their predictions (see [base-esitmator](base-esitmator.md)). Both are "ensembles"; bagging reduces variance, stacking adds a learned combiner.

## Related

- [descision-trees](descision-trees.md) — the base learner.
- [Gradient-boosting](Gradient-boosting.md) — the sequential alternative.
- [cross-validation](cross-validation.md) — OOB makes it partially unnecessary, but still use CV for tuning.
