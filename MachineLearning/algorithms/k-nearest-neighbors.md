# k-Nearest Neighbors (k-NN)

k-NN is the simplest classifier/regressor: **no training at all**. To predict a new point, look at its `k` nearest training points and take a majority vote (classification) or their mean (regression). It's called a *lazy* learner — all the work happens at prediction time.

## The algorithm

1. Compute distance from the query to every training point (usually Euclidean — see the L2 norm in [PLAN](../mathematical-function/PLAN.md)).
2. Take the `k` closest.
3. Classify by majority vote / average.

## Why it's interesting

- **Non-parametric**: no learned weights, no distributional assumption — the "model" is the data itself.
- **Consistent**: as data → ∞ and `k → ∞` appropriately, k-NN error approaches the Bayes optimal — it can represent *any* decision boundary.
- Beautiful in the limit, but cursed in practice (see below).

## The problems

- **Curse of dimensionality**: distance becomes meaningless in high dims — points are all roughly equidistant, so "nearest" is noise. The relative contrast `d_max/d_min` → 1 as dimensions grow. This is exactly why [automated-feature-extraction](../automl/automated-feature-extraction.md) and good features matter.
- **Computational cost**: prediction is O(n·d) unless you build an index (k-d tree, ball tree, HNSW for embeddings).
- **Sensitive to scaling**: an unscaled feature with huge values dominates the distance — always standardize.
- **Imbalanced classes**: majority classes dominate the vote — needs weighting.

## When to use it

- Small, low-dimensional datasets where you want a strong baseline with zero training.
- **Similarity-based retrieval** — the modern survival of k-NN: nearest-neighbor search in learned embedding spaces (see [metric-learning](../automl/metric-learning.md) and [nlp](../nlp/PLAN.md) RAG notes). The "k" in RAG retrieval is k-NN over embeddings.

## Related

- [metric-learning](../automl/metric-learning.md) — learning the distance k-NN uses (the fix for raw-space k-NN).
- [classification](classification.md) — the task; k-NN is the baseline to beat.
- [bias-variance-tradeoff](bias-variance-tradeoff.md) — small `k` = low bias/high variance; large `k` = the reverse.
