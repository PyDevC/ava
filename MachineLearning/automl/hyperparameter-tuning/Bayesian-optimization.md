# Bayesian optimization

Bayesian optimization (BO) is the sample-efficient way to tune black-box functions — like validation accuracy as a function of hyperparameters. Instead of trying many random points ([[random-search]]) it **builds a cheap probabilistic model of the objective** and uses it to decide where to evaluate next.

## The two ingredients

1. **Surrogate model**: a GP (Gaussian Process) — or TPE (Tree Parzen Estimator), or a random forest — that predicts `mean(x)` and `uncertainty(x)` of the objective from past evaluations.
2. **Acquisition function**: scores candidate points by *expected improvement* (or UCB, etc.), balancing
   - **exploitation** (where the surrogate predicts high mean) and
   - **exploration** (where the uncertainty is large).

The loop: `fit surrogate → maximize acquisition → evaluate that point → repeat`. Each new evaluation is *informed* by all previous ones — that's why it needs far fewer runs than grid/random.

## Why it's good

- Works for expensive evaluations (training a model is minutes to hours).
- Handles noisy objectives (CV variance), continuous/discrete/categorical params.
- The standard choice for real tuning (Optuna, Hyperopt, SMAC — used by [[auto-sklearn]]).

## Practical notes

- Start with log-uniform ranges for scale-like params (learning rate, regularization) — uniform ranges waste most points.
- Sequential by nature; parallel variants exist (multi-point acquisition) but are harder.
- Great after a cheap [[random-search]] warm-up to shrink the space.

## Related

- [[random-search]] / [[grid-search]] — the baselines to beat.
- [[Hyperband]] — a different, bandit-based strategy; combined as BOHB.
- [[gradient-based-optimization]] — what BO looks like when the function is smooth enough for gradients (rare for HP tuning).
- [[MachineLearning/automl/auto-sklearn]] — SMAC (BO) is its engine.
