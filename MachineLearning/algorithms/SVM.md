# Support Vector Machines (SVM)

SVMs find a **maximum-margin separating hyperplane**: among all boundaries that separate the classes, pick the one farthest from the nearest training points. The points that touch the margin are the **support vectors** — and they alone define the decision boundary.

## The core idea

For linearly separable data, maximize the margin `2/‖w‖` subject to `yᵢ(w·xᵢ + b) ≥ 1`. This is a constrained quadratic program; the solution depends only on the support vectors (the handful of points on the margin), not all data.

**Soft margin** (`C` parameter): allow some misclassification, trading margin size against violations. Large `C` = fewer violations (lower bias, more overfit); small `C` = wider margin, more tolerance. `C` is the main tuning knob — see [[regularization]] for the same bias/variance reasoning.

## The kernel trick

The big idea: even if data isn't linearly separable in input space, it might be in a **higher-dimensional feature space** (polynomial features, etc.). SVMs let you work implicitly in that space:

- The optimization only ever needs **dot products** `⟨xᵢ, xⱼ⟩`.
- A **kernel** `K(xᵢ, xⱼ)` computes that dot product in feature space *without ever materializing the features*.
- Polynomial / RBF (Gaussian) kernels can separate surprisingly complex data.

This was the classic "I can do nonlinear classification with linear math" trick — conceptually the ancestor of feature learning (see [[neural-network]]).

## Strengths & weaknesses

- **+** Strong theory, works well in moderate-dim problems, robust to overfitting when tuned.
- **−** Scales badly with data (O(n²)–O(n³) training), sensitive to `C` and kernel params, no native probability output (needs Platt scaling), hard to interpret.

## Where it fits today

SVM was the pre-deep-learning default for many classification tasks. For tabular data it's still a solid baseline (see [[tabular-regression]]), but boosting ([[XGBoost]]) and neural nets usually win at scale. The *kernel* idea lives on in kernel methods research.

## Related

- [[classification]] — the task; SVM is one approach.
- [[logistic-regression]] — its probabilistic rival for linear models.
- [[regularization]] — the `C` knob is a regularization tradeoff.
