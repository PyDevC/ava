# Imbalanced classification

Imbalanced classification: the classes you care about are **rare** (fraud, defects, disease, a hand gesture someone makes rarely). Standard accuracy is useless here — predicting "all majority" gives 99% accuracy and zero value.

## The first step: pick the right metric

Accuracy lies. Use metrics that care about the minority class:

- **Precision / Recall / F1** — precision = of my positive predictions, how many were right; recall = of all true positives, how many I caught. F1 = harmonic mean. Trade-off controlled by the decision threshold.
- **PR-AUC** — precision-recall curve area; the right summary for imbalanced data (ROC-AUC can look great while PR is terrible).
- **Confusion matrix** — always look at it; it shows where the failure is (see [[classification]]).

Choose based on cost: if missing a fraud is expensive → high recall (accept false alarms); if false alarms annoy users → high precision.

## Data-level fixes

- **Resampling**: undersample the majority (loses data) or oversample the minority (risk of overfitting; SMOTE synthesizes neighbors instead of duplicating).
- **Class weights**: make the [[loss-function]] penalize minority mistakes more (`class_weight='balanced'`). Cheap and often enough.
- **Ensemble with resampling**: train trees on balanced subsets (EasyEnsemble/BalancedRandomForest) — combines [[bagging-random-forests]] with balance.

## Algorithm/decision fixes

- **Threshold tuning**: a classifier's 0.5 cutoff is arbitrary. Calibrate, then move the threshold to where precision/recall trade off best on validation data (see [[cross-validation]]).
- **Focal loss** (for NNs): down-weight easy examples so training focuses on hard minority cases.
- **One-class / anomaly framing**: when the minority is tiny, treat it as anomaly detection rather than classification.

## Notes

- Stratified folds when cross-validating, or you'll train on folds with zero positives (see [[cross-validation]]).
- Imbalanced data is not *inherently* broken — trees/boosting often handle moderate imbalance fine; the problem is mainly evaluation + extreme imbalance.

## Related

- [[classification]] — the task and its eval metrics.
- [[cross-validation]] — stratification matters most here.
- [[loss-function]] — where class weights and focal loss plug in.
