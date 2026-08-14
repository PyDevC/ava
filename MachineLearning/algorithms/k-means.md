# k-means

k-means is the canonical **unsupervised clustering** algorithm: partition the data into `k` clusters, each represented by its centroid (mean), minimizing the within-cluster squared distance.

```
minimize  Σⱼ Σ_{x ∈ Cⱼ} ‖x - μⱼ‖²
```

## The algorithm (Lloyd's)

1. Pick `k` initial centroids (k-means++ seeding is the standard fix).
2. **Assign**: put each point in the cluster of its nearest centroid.
3. **Update**: recompute each centroid as the mean of its cluster.
4. Repeat 2–3 until assignments stop changing.

Each step monotonically decreases the objective, so it always converges — but to a *local* optimum (bad seeding = bad clusters). Running several restarts and keeping the best objective is standard.

## The big questions

- **How to pick `k`?** The elbow method (plot objective vs `k`, look for a bend), the silhouette score, or domain knowledge. There's no free lunch — see [hyperparameter-tuning](../automl/hyperparameter-tuning.md) for the search mindset.
- **Feature scaling matters hugely** — k-means is distance-based, so unscaled features dominate. Standardize (see [k-nearest-neighbors](k-nearest-neighbors.md)'s same caveat).
- **Shapes**: k-means finds spherical, same-size clusters. Non-convex or elongated clusters need DBSCAN / hierarchical clustering.

## Strengths & weaknesses

- **+** Fast, simple, scales to huge data, gives a hard partition.
- **−** Needs `k` up front, sensitive to initialization and outliers, biased toward equal-size spherical clusters.
- **−** Not robust to noise — outliers drag centroids. k-medoids (use data points as centers) fixes this.

## Relation to other things

- The assignment/update loop is **expectation-maximization (EM)** on a Gaussian-mixture model where the covariance is fixed to identity — k-means is the hard-assignment special case of GMM.
- As a preprocessing step, cluster assignments are cheap [automated-feature-extraction](../automl/automated-feature-extraction.md) (cluster-ID features).
- [SOM](SOM.md) is the topology-preserving cousin: k-means without the grid constraint.

## Related

- [SOM](SOM.md) — the self-organizing map alternative for visualization.
- [classification](classification.md) — unsupervised vs supervised: clustering has no labels.
- [bias-variance-tradeoff](bias-variance-tradeoff.md) — picking `k` is the same complexity-tradeoff decision.
