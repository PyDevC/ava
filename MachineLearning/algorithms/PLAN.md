# MachineLearning/algorithms — Plan

## Goal of this directory

Cover the **core algorithms and modeling building blocks**: how the main ML algorithms work, their strengths/weaknesses, and the pieces (loss, cost, estimators) they're built from. Everything here should be *implementable from memory* — theory + intuition, not library docs.

## What's already here

- Boosting family: [[Gradient-boosting]], [[XGBoost]], [[LightGBM]], [[base-esitmator]]
- Trees: [[descision-trees]]
- Linear-ish: [[weighted-linear-regression]], [[tabular-regression]], [[cost-function]], [[loss-function]], [[regularization]], [[logistic-regression]]
- Tasks: [[classification]], [[ranking]], [[imbalanced-classification]]
- Ensembles: [[bagging-random-forests]], [[stacking]]
- NN: [[neural-network]], [[transfer-learning]], [[Universal-approximation-function]], [[injectivity]]
- Unsupervised: [[SOM]], [[k-means]], [[PCA]]
- Lazy/similarity: [[k-nearest-neighbors]], [[SVM]], [[naive-bayes]]
- Concepts: [[bias-variance-tradeoff]], [[cross-validation]]

## What to add next

- [x] **Missing classic algorithms**:
  - [x] Logistic regression (the classification workhorse — currently only implied via loss/softmax notes)
  - [x] k-Nearest Neighbors (lazy learning, curse of dimensionality)
  - [x] Support Vector Machines (max margin, kernels, dual)
  - [x] Naive Bayes (generative model, independence assumption)
  - [x] k-means + hierarchical clustering (only SOM covers unsupervised now)
  - [x] PCA / dimensionality reduction (see [[injectivity]] for the info-preservation framing)
- [x] **Model behavior concepts**:
  - [x] Bias-variance tradeoff, underfitting/overfitting
  - [x] Regularization: L1/L2/elastic net (extends [[cost-function]]'s penalty mention)
  - [x] Cross-validation and the train/val/test split discipline (extends [[classification]]'s eval section)
  - [x] Imbalanced classification (resampling, class weights, PR-vs-ROC)
- [x] **Ensembles beyond boosting**:
  - [x] Bagging + random forests (the diversity story vs boosting)
  - [x] Stacking / voting (mentioned in [[base-esitmator]])
- [x] **Interpretability** (grows in importance):
  - [x] Feature importance (tree-based), SHAP/permutation — ties to [[ranking]] and tabular work

## Prioritization

1. **Logistic regression** — the single most-used classifier, weirdly missing.
2. **Bias-variance + regularization + CV** — the concepts that make all other notes click.
3. **k-NN / SVM / Naive Bayes / k-means** — the classic four to round out breadth.
