# Random search

Random search samples hyperparameter configurations **uniformly at random** from the specified distributions (instead of trying a fixed Cartesian grid like [[grid-search]]).

## Why it's usually better than grid search (Bergstra & Bengio 2012)

The key insight: in most problems, **only a few hyperparameters actually matter**, and their optimum is a small region of the space.

- Grid search wastes most evaluations on *unimportant* parameters (it spends the same budget on every combination, including bad corners).
- Random search spreads points evenly over each dimension, so it *hits the important parameter's good region* with probability proportional to its size — regardless of how many unimportant params exist.

With the same budget, random search finds better (or equal) optima in high dimensions.

## Practical setup

- Define distributions per parameter: `uniform(low, high)`, `log-uniform` (for scales like learning rate / regularization), `choice([...])`, `int`.
- Pick `n` iterations = budget; run each config with CV; keep the best.
- Trivial to parallelize (embarrassingly independent).

## Pros & cons

- **+** Beats grid in high dims, trivial to implement, gives good results fast.
- **+** Can be refined later by narrowing ranges around the best point (sequential refinement).
- **−** No memory: doesn't learn from past evaluations → still sample-inefficient vs [[hyperparameter-tuning/Bayesian-optimization]].

## Related

- [[grid-search]] — the brute-force baseline.
- [[hyperparameter-tuning/Bayesian-optimization]] — the sample-efficient sequential successor.
- [[random-search]] is what many libraries' `random_search` / `RandomizedSearchCV` implement.
