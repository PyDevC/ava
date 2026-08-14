# XGBoost

XGBoost (eXtreme Gradient Boosting) is a highly optimized, scalable implementation of gradient boosted decision trees. It's been the go-to for tabular data / structured problems (kaggle) because it's fast, robust, and handles missing values natively.

## What it adds over plain gradient boosting

- **Second-order gradients**: uses both first (`g`) and second (`h`) derivatives of the loss, so the split scoring becomes a Newton-like step — faster convergence, better objective.
- **Regularized objective**:
  ```
  obj = Σ L(y_i, ŷ_i) + γ·T + ½λ·Σw_j² + α·Σ|w_j|
  ```
  `T` = number of leaves, `w_j` = leaf weights. This penalizes complexity directly and is why XGBoost rarely overfits badly.
- **Weighted quantile sketch** — approximate split finding for huge datasets.
- **Column (feature) subsampling** — like random forests, decorrelates trees.
- **Shrinkage (learning rate)** and row subsampling as additional regularization.
- **Missing value handling** — learns the best direction to route NaNs.
- **Cache-aware block structure** for training data + out-of-core computation.

## Why it was so dominant

Before neural nets took over unstructured data, XGBoost won almost every tabular competition. It's still the default first model for many tabular pipelines, and it's what [[Gradient-boosting]] looks like when you care about engineering + speed.

## Roughly how it's trained

1. Start with a constant prediction.
2. For each boosting round, compute `g_i` and `h_i` for every sample (gradient/hessian of loss).
3. Grow a tree greedily: for each split, the gain is a closed-form function of the sums of `g` and `h` on each side — this is the "exact greedy" vs "approx" (quantile sketch) choice.
4. Prune (γ threshold) and add the tree to the ensemble with shrinkage factor.

Related: [[LightGBM]] is the leaf-wise competitor, [[base-esitmator]] explains the estimator abstraction.
