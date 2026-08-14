# Optuna

Optuna is the de-facto Python hyperparameter tuning library — the practical face of the [[Bayesian-optimization]]/[[Hyperband]] theory. Its two superpowers over older tools: a **define-by-run API** and **pruning** built in.

## The define-by-run API

```python
import optuna

def objective(trial):
    lr     = trial.suggest_float("lr", 1e-5, 1e-1, log=True)
    hidden = trial.suggest_int("hidden", 64, 512, step=64)
    model = build_model(hidden)
    return train_and_eval(model, lr)

study = optuna.create_study(direction="minimize")
study.optimize(objective, n_trials=100)
```

- `suggest_*` inside the objective = the search space is **described while the trial runs** — conditional spaces (only sample `dropout` if `use_dropout=True`), dynamic ranges, and nested trials all work naturally (vs static config dicts in older tools).
- Each `suggest_*` call reports a parameter → the sampler records it → next trials use the history.

## The default sampler: TPE

Optuna defaults to **TPE** (Tree-structured Parzen Estimator, see [[Bayesian-optimization]]): it models `p(x | good)` and `p(x | bad)` from observed trials and proposes points where the good/bad ratio is highest. `create_study(sampler=optuna.samplers.TPESampler(n_startup_trials=10))` — the startup random trials bootstrap the surrogate.

## Pruning — the killer feature

**Pruning** = terminate a bad trial *mid-run* (successive-halving style, see [[Hyperband]] and [[ASHA]]):

```python
for epoch in range(epochs):
    val = train_epoch()
    trial.report(val, epoch)
    if trial.should_prune(): raise optuna.exceptions.TrialPruned()
```

- With `optuna.integration.PyTorchLightningPruningCallback` (or plain `report`/`should_prune`), a trial that's clearly losing is killed early → the budget goes to promising trials.
- `create_study(pruner=optuna.pruners.MedianPruner())` is the safe default; `HyperbandPruner` pairs with the theory.
- `study.best_trial` gives the best params + `best_trial.value`; `study.trials_dataframe()` for export.

## Storage & distribution

- `study = optuna.create_study(storage="sqlite:///db.sqlite")` — persist to SQL; `study = optuna.load_study(...)` resumes. MySQL/PostgreSQL for parallel search: multiple workers run `study.optimize` on the same storage.
- **No locking issues** in practice; each worker does its own sampling from shared history.

## Related

- [[Bayesian-optimization]] — the TPE theory.
- [[Hyperband]] / [[ASHA]] — the pruning machinery.
- [[MachineLearning/automl/PLAN]] — where Optuna sits vs AutoGluon/auto-sklearn.
- [[../algorithms/cross-validation]] — the eval protocol inside each trial.
