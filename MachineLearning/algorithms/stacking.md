# Stacking

Stacking (stacked generalization) combines **diverse base models** by training a **meta-model** on their predictions. Unlike [bagging-random-forests](bagging-random-forests.md) (averaging same-type models), stacking learns *how much to trust* each model per prediction.

## The architecture

```
Level 0 (base models):  M1(x), M2(x), M3(x)   — different algorithms
Level 1 (meta-model):   F(M1(x), M2(x), M3(x)) — learns to combine
```

The base models can be anything: trees, linear, k-NN, NN (see [base-esitmator](base-esitmator.md)). The meta-model is usually a simple logistic regression / small tree.

## The critical detail: no leakage

If the meta-model is trained on the base models' *training-set* predictions, it overfits — those predictions are too optimistic. The correct pattern:

1. Split training data into folds (see [cross-validation](cross-validation.md)).
2. For each fold: train base models on the other folds, predict this fold **out-of-fold**.
3. The **out-of-fold predictions** become the meta-features; train the meta-model on them.
4. Refit base models on all data for real deployment.

This is exactly the "nested CV" discipline from [cross-validation](cross-validation.md), applied inside stacking.

## When it shines

- When models make **complementary errors** (different families, different biases) — stacking exploits that.
- AutoML uses it heavily: the "ensemble" in [h2o-automl](../automl/h2o-automl.md)'s leaderboard and [auto-sklearn](../automl/auto-sklearn.md)'s stacking are this pattern.
- It reliably beats the best single model in practice (often a small but real gain).

## Costs

- More compute (many base models + CV).
- Needs good cross-validation to avoid the leakage trap above.
- Less interpretable than a single model.

## Related

- [base-esitmator](base-esitmator.md) — the base models being stacked.
- [bagging-random-forests](bagging-random-forests.md) — the variance-reduction ensemble (vs stacking's learned combiner).
- [cross-validation](cross-validation.md) — the machinery that makes stacking honest.
