# Hyperband

Hyperband is a **bandit-based** hyperparameter tuning strategy that dramatically cuts compute by *early-stopping* bad configurations early, while spending more budget on the promising ones. It's the "successive halving" idea taken to its logical end.

## Successive halving first

Given a budget `B` per config and `n` configs:

1. Evaluate all `n` configs on a small fraction `B/n` of the budget.
2. Keep the best half (halving), give them double the budget each round.
3. Repeat until one config remains.

This is efficient *if* you know how many configs to start with — guess too many and everything dies too early (no config ever gets enough budget); guess too few and you under-explore.

## How Hyperband fixes that

Hyperband **brackets** the problem: it runs successive halving with *different starting budgets* in parallel (the "brackets"). Each bracket covers a different exploration/exploitation trade-off:

- Bracket with many configs × tiny initial budget → aggressive exploration.
- Bracket with few configs × large initial budget → exploitation.

The union of brackets means good configs get found whether they're quick winners or need full budget to shine — without you guessing the right starting point.

## Why it matters

- Near-optimal anytime performance on black-box tuning (the paper shows it beats both pure random and Bayesian methods at fixed budgets, especially in parallel settings).
- Cheap enough to be practical with expensive models (deep learning).
- Often combined with Bayesian optimization: **BOHB** = Bayesian Optimization + Hyperband (use the surrogate to *choose* which configs each bracket evaluates).

## Related

- [random-search](random-search.md), [grid-search](grid-search.md) — the naive baselines it outperforms.
- [bayesian-optimization](bayesian-optimization.md) — the smarter sampling strategy Hyperband can wrap.
- [hyperparameter-search](../../deeplearning/hyperparameter-search.md) — where you'd actually use it.
