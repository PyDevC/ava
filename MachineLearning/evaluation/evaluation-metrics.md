# Evaluation metrics

Evaluation is choosing the right score for the task — and then trusting it. This note is a catalog of the common metrics by task type, the cross-validation strategies that give them meaning, and the leakage pitfalls that quietly invalidate them.

## By task type

**Classification** (see [classification](../algorithms/classification.md)):
- **Accuracy** — fraction correct. Misleading when classes are imbalanced (99% "no" gives 99% accuracy with a do-nothing model — see [imbalanced-classification](../algorithms/imbalanced-classification.md)).
- **Precision / Recall / F1** — precision = of predicted positives, how many real; recall = of real positives, how many found; F1 = harmonic mean. The right view for imbalanced or cost-skewed problems.
- **ROC / AUC** — true-positive vs false-positive rate across thresholds; threshold-independent but optimistic under imbalance.
- **PR-AUC** — precision vs recall across thresholds; the right curve when positives are rare.
- **Log loss** — the probabilistic score: how well the model's *confidences* track reality (see [loss-function](../algorithms/loss-function.md)).

**Regression** (see [tabular-regression](../algorithms/tabular-regression.md)):
- **MAE** — mean absolute error; robust to outliers, in the target's units.
- **MSE / RMSE** — squared error penalizes big misses; RMSE is in the target's units. Squaring makes it outlier-sensitive.
- **R²** — fraction of variance explained (baseline = predict the mean).

**Ranking** (see [ranking](../algorithms/ranking.md)): **NDCG** (position-discounted relevance, normalized) and **MAP** (mean average precision at top positions).

**Segmentation** (see [segmentation](../computer-vision/segmentation.md)): **IoU/mIoU** (intersection over union per class, averaged) and **Dice** (`2|P∩T|/(|P|+|T|)`) — the class-imbalance-friendly alternative.

**Object detection** (see [object-detection](../computer-vision/object-detection.md)): **mAP** — mean average precision over classes and IoU thresholds (COCO's `mAP@[.5:.95]` is the standard).

## Cross-validation strategies

- **k-fold** — the default (5 or 10); mean ± std of fold scores.
- **Stratified k-fold** — preserves class proportions per fold; required for imbalanced classification.
- **Group k-fold** — keeps related rows (one subject, one session) together; crucial for the gesture data in [hestreg](../computer-vision/hestreg.md), where frames from one recording are not independent.
- **Time-series split** — rolling origin; never random-shuffle temporally ordered data.

See [cross-validation](../algorithms/cross-validation.md) for the discipline; the point is the metric is only as good as the split it's computed on.

## Leakage pitfalls

- **Preprocessing leakage**: fit scalers/imputation/encoders on the *full* dataset, then split — validation folds have already seen the training distribution. Always fit on the training split only.
- **Temporal leakage**: random-splitting data that has an order (logs, sensor streams, video frames) lets future data train on past and inflates scores.
- **Duplicate / near-duplicate samples across folds**: the same image (or augmented copy) in both train and validation — the model "memorized" its test. De-duplicate before splitting.
- **Feature leakage**: using columns that encode the target (a target-encoded field, or a "future value" in the row). Same family as [model-interpretability](../algorithms/model-interpretability.md)'s "am I really predicting what I think".

## Related

- [cross-validation](../algorithms/cross-validation.md) — how the metric is computed reliably.
- [ranking](../algorithms/ranking.md) — NDCG/MAP definitions.
- [model-interpretability](../algorithms/model-interpretability.md) — the "is the model actually doing what the metric says" check.
- [classification](../algorithms/classification.md) / [loss-function](../algorithms/loss-function.md) — the losses the metrics summarize.
