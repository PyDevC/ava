# ASHA, PBT, BOHB

The modern **multi-fidelity** tuning methods — the successors to plain [Hyperband](Hyperband.md) for deep learning, where each evaluation is expensive and you want to stop losers early and keep winners training.

## ASHA — Asynchronous Successive Halving

- **Successive halving** (the Hyperband core): run N candidates for a *fraction* of budget, promote the top fraction, repeat. ASHA makes it **asynchronous**: workers pull the next-promote decision whenever they finish, so no worker idles waiting for a synchronized round (perfect for shared clusters).
- `asha_pruner`/`HyperbandPruner` in Optuna implement it (see [optuna](optuna.md)); it's also the default in Ray Tune.
- **When**: many cheap-to-evaluate candidates (small models, few epochs), parallel workers, "tune with limited budget" mindset.

## PBT — Population Based Training

- **Evolutionary**: run a *population* of models in parallel; each trains normally, but periodically the losers **inherit weights + hyperparameters from the winners** (exploit) with random perturbation (explore).
- The key difference from ASHA/BOHB: **the models keep training** — no restarts. This is what makes PBT the standard for expensive runs (AlphaZero, large RL, LLM LR schedules). It *adapts* hyperparameters during training (e.g. LR decays over the run) rather than fixing them up front.
- **When**: training is long enough that the *schedule* of HPs matters more than finding one static value; you can afford N concurrent runs.
- `optuna.integration` has no PBT, but Ray Tune / `pbt` libs do; the "inheritance" trick is what's hard to get right (you're copying weights, not just params).

## BOHB — Bayesian Optimization + Hyperband

- **Hyperband's** early-stopping structure, but the *candidate generation* uses a **Bayesian surrogate** (a TPE-like model, see [Bayesian-optimization](Bayesian-optimization.md)) instead of random draws.
- At each budget level it fits a model on the surviving candidates' results → next candidates come from the model, not the uniform prior → sample-efficient AND multi-fidelity.
- **When**: you want Hyperband's budget savings but candidates are so expensive that blind random sampling wastes too much (mid-large NNs).

## The decision rule

| Method | Budget model | Restart? | Parallel-friendly | Best for |
|---|---|---|---|---|
| ASHA | successive halving | yes | very | many cheap runs |
| PBT | evolutionary | no (keeps training) | yes | long expensive runs, adaptive HPs |
| BOHB | halving + BO surrogate | yes | medium | expensive candidates + early stopping |

All three assume the **early budget correlates with final quality** — the "multi-fidelity" assumption. When early performance is uncorrelated (e.g. loss explodes then recovers), pruning schemes fire wrongly.

## Related

- [Hyperband](Hyperband.md) — the base they extend.
- [Bayesian-optimization](Bayesian-optimization.md) — BOHB's surrogate.
- [optuna](optuna.md) — where ASHA/BOHB/pruning are used day-to-day.
- [model-optimization](../../deeplearning/model-optimization.md) — PBT adapts LR schedules at training time.
