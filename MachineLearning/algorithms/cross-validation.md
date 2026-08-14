# Cross-validation

Cross-validation (CV) estimates how well a model generalizes by **training and evaluating on different splits** of the data — so you can pick hyperparameters and detect overfitting *without* touching the test set (see [bias-variance-tradeoff](bias-variance-tradeoff.md)).

## The discipline: train / validation / test

- **Training set** — fit the model.
- **Validation set** — pick hyperparameters and compare models (searched by cross-validation).
- **Test set** — evaluate ONCE at the very end. If you use it for search, you've overfit to it.

The test set is the "exam"; validation is "practice exams". Looking at the exam before taking it is data leakage.

## K-fold cross-validation

1. Split training data into K equal folds.
2. For each fold: train on the other K−1, evaluate on this fold.
3. Report mean (± std) of the K scores.

K = 5 or 10 are the defaults. **Stratified K-fold** keeps class proportions per fold — important for imbalanced classification. **Group K-fold** keeps groups together (e.g. all rows of one subject in the same fold) — crucial for the gesture/behavior data in [computer-vision](../computer-vision/).

## Variants

- **Repeated K-fold** — run K-fold several times with different shuffles, average (reduces variance of the estimate).
- **LOOCV** (leave-one-out) — K = n; expensive but uses max data.
- **Time-series CV** — rolling origin; *never* random shuffle when rows have temporal order (leakage: future rows inform the past).
- **Nested CV** — an outer CV for model comparison, inner CV for hyperparameters (avoids optimistic estimates when you tune inside CV).

## Common pitfalls

- **Shuffling leaks**: split before any preprocessing that uses the full dataset (scaling, imputation, target encoding) — otherwise the validation folds saw the training distribution.
- **P-hacking the test set**: many runs against the same test split erodes its value.
- Reporting the CV mean as if it were test performance — it's an estimate, not the final score.

## Related

- [bias-variance-tradeoff](bias-variance-tradeoff.md) — CV is how you locate the sweet spot.
- [regularization](regularization.md) — `λ` selection is the classic CV use.
- [classification](classification.md) / [tabular-regression](tabular-regression.md) — where the discipline gets applied.
