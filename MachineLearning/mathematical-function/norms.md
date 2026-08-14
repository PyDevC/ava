# Norms (L1, L2, L∞)

A norm measures the **size** of a vector. The p-norm:

```
‖x‖_p = (Σ |xᵢ|^p)^(1/p)
```

## The three you actually use

- **L2 / Euclidean**: `‖x‖₂ = √(Σ xᵢ²)`. The familiar distance; rotation-invariant, smooth, gives the closed-form solutions in linear algebra. Penalty form = **ridge** (see [regularization](../algorithms/regularization.md)).
- **L1 / Manhattan**: `‖x‖₁ = Σ|xᵢ|`. Sum of absolute values; prefers sparse solutions (induces exactly-zero weights = feature selection). Non-smooth at 0 → needs subgradient/proximal methods.
- **L∞ / max**: `‖x‖∞ = max|xᵢ|`. Only the largest entry matters; used in robust bounds (e.g. adversarial perturbations bounded by L∞ box).

The relationship: `‖x‖∞ ≤ ‖x‖₂ ≤ ‖x‖₁ ≤ √d·‖x‖₂` (d = dims). As p grows, the norm cares more about the single largest entry.

## Where they show up

- **Distance functions** — k-NN, k-means, and clustering all need a metric (see [k-nearest-neighbors](../algorithms/k-nearest-neighbors.md), [k-means](../algorithms/k-means.md)).
- **Regularization** — L2 shrinks all weights smoothly; L1 drives some to zero (see [regularization](../algorithms/regularization.md)). Elastic net = both.
- **Lipschitz constants** — the Lipschitz constant of a linear map is its operator norm `‖W‖` = largest singular value (see [lipsschitz-function](lipsschitz-function.md)). Spectral normalization enforces exactly this.
- **Adversarial robustness** — perturbation budgets are balls: L2 ball, L∞ box.
- **Normalization** — dividing by a norm (L2-normalize embeddings) makes cosine similarity meaningful (see [metric-learning](../automl/metric-learning.md)).

## Related

- [linear-function](linear-function.md) — norms measure the output of linear maps (operator norm).
- [regularization](../algorithms/regularization.md) — the practical consumer of L1/L2.
- [lipsschitz-function](lipsschitz-function.md) — the operator-norm connection.
