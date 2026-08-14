# LR schedulers

These are the functions that dynamically adjust the learning rate.

Which means that we can have adjustable learning rate so in the starting phase of training we can have higher learning rates and during ending phase we can have lower for fine tuning.

When used correctly it generally gives better accuracy.

## Why schedule the LR

- **High LR early** — explore fast, escape bad basins, converge quickly.
- **Low LR late** — fine-tune, settle into a sharper-but-deeper minimum, avoid oscillating around the optimum.
- Blindly constant LR is a compromise that's wrong at both ends.

## The common schedulers (PyTorch)

- **StepLR** — multiply LR by `gamma` every `step_size` epochs. Simple, classic.
- **MultiStepLR** — decay at specific epochs (e.g. ×0.1 at epoch 30, 60, 90 — the old ResNet recipe).
- **ExponentialLR** — `lr = lr0 · gamma^epoch`. Smooth continuous decay.
- **CosineAnnealingLR** — cosine decay to ~0 over `T_max` epochs; the modern default for deep nets (plus warm restart variants like **CosineAnnealingWarmRestarts**).
- **ReduceLROnPlateau** — decay when a monitored metric stops improving. Great when you don't know the schedule in advance.
- **OneCycleLR** — warm up then anneal within each cycle; the fastai "super-convergence" trick.

## Usage pattern

```python
optimizer = torch.optim.SGD(model.parameters(), lr=0.1)
scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=epochs)

for epoch in range(epochs):
    train_one_epoch(...)
    scheduler.step()   # after epoch (or after batch for OneCycle)
```

## Notes

- **Warmup** (ramp LR up for the first few hundred steps) helps huge-batch and transformer training a lot — prevents early divergence.
- The LR schedule is itself a set of [hyperparameter-search](hyperparameter-search.md) targets; see [gradient-based-optimization](../automl/hyperparameter-tuning/gradient-based-optimization.md) for tuning it automatically.
