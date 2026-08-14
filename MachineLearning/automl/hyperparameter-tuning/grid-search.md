# Grid search

Grid search is the simplest [[hyperparameter-tuning]] strategy: define a **fixed set of values per hyperparameter**, try **all combinations** (Cartesian product), pick the best by cross-validation score.

## Example

```
max_depth:  [3, 5, 7]
n_estimators: [100, 300]
→ 3 × 2 = 6 full model trainings
```

Each config is evaluated with CV and the best-scoring one wins (with the model refit on the full training data).

## Pros & cons

- **+** Simple, exhaustive, easy to parallelize.
- **+** Deterministic and reproducible.
- **−** Curse of dimensionality: `k` params with `n` values each = `n^k` runs — explodes fast.
- **−** Wastes budget on irrelevant regions; a bad grid placement (wrong range) can miss the optimum entirely.
- **−** Independent ranges miss parameter *interactions* (e.g. high depth + low min-samples is fine, high depth + high min-samples is fine — but grid samples the corners badly).

## When to use it

- Small parameter spaces (≤ 2–3 dims), cheap models.
- As a first sanity baseline before [[random-search]] or [[hyperparameter-tuning/Bayesian-optimization]].

## Related

- [[random-search]] — same idea but samples randomly; works better in high dims.
- [[hyperparameter-tuning/Bayesian-optimization]] — the smarter, sequential version.
- In sklearn: `GridSearchCV`.
