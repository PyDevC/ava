# MachineLearning/algorithms — Plan

## Goal of this directory

Cover the **core algorithms and modeling building blocks**: how the main ML algorithms work, their strengths/weaknesses, and the pieces (loss, cost, estimators) they're built from. Everything here should be *implementable from memory* — theory + intuition, not library docs.

## What's already here

- Boosting family: [[Gradient-boosting]], [[XGBoost]], [[LightGBM]], [[base-esitmator]]
- Trees: [[descision-trees]]
- Linear-ish: [[weighted-linear-regression]], [[tabular-regression]], [[cost-function]], [[loss-function]]
- Tasks: [[classification]], [[ranking]]
- NN: [[neural-network]], [[transfer-learning]], [[Universal-approximation-function]], [[injectivity]]
- Unsupervised: [[SOM]]

## What to add next

- [ ] **Missing classic algorithms**:
  - [ ] Logistic regression (the classification workhorse — currently only implied via loss/softmax notes)
  - [ ] k-Nearest Neighbors (lazy learning, curse of dimensionality)
  - [ ] Support Vector Machines (max margin, kernels, dual)
  - [ ] Naive Bayes (generative model, independence assumption)
  - [ ] k-means + hierarchical clustering (only SOM covers unsupervised now)
  - [ ] PCA / dimensionality reduction (see [[injectivity]] for the info-preservation framing)
- [ ] **Model behavior concepts**:
  - [ ] Bias-variance tradeoff, underfitting/overfitting
  - [ ] Regularization: L1/L2/elastic net (extends [[cost-function]]'s penalty mention)
  - [ ] Cross-validation and the train/val/test split discipline (extends [[classification]]'s eval section)
  - [ ] Imbalanced classification (resampling, class weights, PR-vs-ROC)
- [ ] **Ensembles beyond boosting**:
  - [ ] Bagging + random forests (the diversity story vs boosting)
  - [ ] Stacking / voting (mentioned in [[base-esitmator]])
- [ ] **Interpretability** (grows in importance):
  - [ ] Feature importance (tree-based), SHAP/permutation — ties to [[ranking]] and tabular work

## Prioritization

1. **Logistic regression** — the single most-used classifier, weirdly missing.
2. **Bias-variance + regularization + CV** — the concepts that make all other notes click.
3. **k-NN / SVM / Naive Bayes / k-means** — the classic four to round out breadth.
