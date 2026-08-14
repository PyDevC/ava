# hyperparameter-tuning — Plan

## Goal of this directory

Cover the **hyperparameter tuning strategies** — the search algorithms that AutoML tools run on the inside. The goal: know when to use which strategy (grid → random → Bayesian → bandit), and be able to explain the trade-off between sample-efficiency and compute per evaluation.

## What's already here

- [[grid-search]] — exhaustive Cartesian search.
- [[random-search]] — uniform sampling; beats grid in high dims (Bergstra & Bengio).
- [[Bayesian-optimization]] — GP/TPE surrogate + acquisition; the sample-efficient workhorse.
- [[Hyperband]] — successive halving / bandit; early-stopping for many cheap candidates.
- [[gradient-based-optimization]] — differentiating through training for continuous HPs.
- [[optuna]] — the de-facto tool (TPE sampler, pruning, define-by-run).
- [[asha-pbt-bohb]] — the modern multi-fidelity methods.
- [[tuning-methodology]] — search-space design, budgets, seeds, multi-objective.

## What to add next

- [x] **The implementations** (how it's actually used):
  - [x] **Optuna** sampler walkthrough (TPE by default) + pruning — the tool I'll actually use
  - [x] SMAC (auto-sklearn's engine) vs TPE — comparing the two dominant surrogates → see [[Bayesian-optimization]] + [[optuna]]
  - [x] scikit-learn `GridSearchCV` / `RandomizedSearchCV` mapping to these notes → they are literally [[grid-search]]/[[random-search]] with a CV split inside
- [x] **Advanced multi-fidelity methods** (beyond plain Hyperband):
  - [x] ASHA (Asynchronous Successive Halving) — the parallel-friendly version
  - [x] PBT (Population Based Training) — evolutionary, used for large NN training
  - [x] BOHB (BO + Hyperband hybrid)
- [x] **Practical methodology**:
  - [x] Warm-starting and search-space design (log-uniform ranges for scales)
  - [x] Multi-objective tuning (accuracy + latency), Pareto fronts
  - [x] When tuning is pointless (few evals, cheap training → random search wins)
- [ ] **Reproducibility** — seeds, fixed search budgets, reporting best-config-with-metrics (ties to eval discipline in [[../algorithms]]) → covered in [[tuning-methodology]]

## Prioritization

1. **Optuna + practical methodology** — most of my tuning will happen there.
2. **ASHA / PBT / BOHB** — the modern methods used in deep learning.
3. **SMAC vs TPE** — only if I need to explain the internals of the AutoML tools.
