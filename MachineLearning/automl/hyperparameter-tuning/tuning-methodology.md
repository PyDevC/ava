# Tuning methodology and search-space design

Most tuning projects fail on **methodology**, not on the sampler: a badly designed search space, no seeds, or a meaningless budget. This note is the "how to actually tune" companion to the algorithm notes ([grid-search](grid-search.md), [random-search](random-search.md), [bayesian-optimization](bayesian-optimization.md)).

## Search-space design (the highest-leverage step)

- **Use log-uniform for scale parameters**: learning rates, regularizers, network sizes live across orders of magnitude — `lr ~ U(-5, -1)` in log10 is right; uniform in raw value wastes most samples on the wrong range. Optuna: `suggest_float(..., log=True)` (see [optuna](optuna.md)).
- **Sample around the reference point**: if a published/previous run used `lr=3e-4`, center the range there (1e-4…1e-3) instead of the full academic span.
- **Only tune what matters**: structure (layers, heads, dropout, normalization) >> scalar knobs. Measure sensitivity first (one-at-a-time or a fast random run), then search the few that move the needle.
- **Conditional spaces** (only sample `dropout` when a dropout layer exists) — define-by-run APIs (Optuna) make this trivial.

## Warm-starting and budgets

- **Warm start**: `study.optimize(...)` resumes from a previous storage (or pass `init` trials) — never throw away history; transfer a tuned config to a new dataset as a *center* for a narrower search.
- **Budget = samples × cost per sample**. Rule of thumb: fewer than ~20–30 total evaluations → **random search** ([random-search](random-search.md)) beats Bayesian (the surrogate has nothing to learn); 30–300 → BO/TPE; with cheap epochs → add pruning/ASHA ([asha-pbt-bohb](asha-pbt-bohb.md)).
- Fix **total compute**, not trial count, when pruning is involved — pruning changes what "n_trials" means.

## Multi-objective tuning

- Optimize **two objectives** (accuracy + latency) → Pareto front (`optuna.create_study(directions=["maximize", "minimize"])`). The front *is* the answer — every point is "best" for some accuracy/latency trade-off.
- Combine into one scalar only if you have a real cost model (e.g. `accuracy − λ·latency`); otherwise you're hiding the trade-off.
- Constraint-objectives (keep latency < 50ms) work too: `study = optuna.create_study(...); trial.set_user_attr` / the `constraints_func` API.

## Reproducibility — non-negotiable

- **Seeds**: fix the seed *per trial* (`seed = trial.number`) so a config maps to a deterministic result — otherwise tuning looks like it found improvement when it just caught variance.
- **Report best config WITH its metric + the seed + the budget** — a tuned config without its eval protocol is untrustworthy (ties to the eval discipline in [cross-validation](../../algorithms/cross-validation.md)).
- **Honest comparison**: same train/val split across trials (a single fixed split, or a fixed CV fold scheme), same eval metric.

## When tuning is pointless

- Training is cheap and data small → random search + cross-validation beats the engineering time.
- The metric is dominated by variance (noisy data, tiny eval set) → tune *variance reduction* first (bigger eval set, seed averaging).
- You have one evaluation budget and no historical knowledge → just run the sensible defaults.

## Related

- [grid-search](grid-search.md) / [random-search](random-search.md) / [bayesian-optimization](bayesian-optimization.md) — the algorithms.
- [asha-pbt-bohb](asha-pbt-bohb.md) — multi-fidelity options.
- [optuna](optuna.md) — the practical driver.
- [cross-validation](../../algorithms/cross-validation.md) — the eval protocol each trial needs.
