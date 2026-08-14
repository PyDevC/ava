# Hyperparameter Search

Hyperparameters are the options in a model that can be tuned to get the best performance with best efficiency out of the model.

Choosing the Hyperparameters for the model can be challenging and choosing a close enough hyperparameter comes with experience.

## What is a hyperparameter (vs a parameter)

- **Parameters**: learned from data during training (weights `w`, biases).
- **Hyperparameters**: set *before* training — learning rate, batch size, number of layers/units, optimizer choice, dropout, weight decay... They control the learning process, and you can't get them from the data directly (see [hyperparameter-tuning](../automl/hyperparameter-tuning.md)).

## The important ones for deep learning

- **Learning rate** — the single most impactful one. Too high → diverges, too low → slow. Needs a [how-to-use-lr-scheduler](how-to-use-lr-scheduler.md) to decay over time.
- **Batch size** — interacts with LR (larger batches usually want larger LR); affects memory and convergence.
- **Optimizer choice + its params** (momentum, Adam betas, weight decay).
- **Architecture**: depth, width, dropout, activation.
- **Regularization**: dropout rate, weight decay, augmentation.

## How to search

- **Manual / experience-based** — decent starting points from prior knowledge, then iterate. Still the practical norm.
- **[grid-search](../automl/hyperparameter-tuning/grid-search.md) / [random-search](../automl/hyperparameter-tuning/random-search.md)** — brute force baselines; random usually beats grid in high dims.
- **[bayesian-optimization](../automl/hyperparameter-tuning/bayesian-optimization.md)** — sample-efficient, the smart choice for expensive runs.
- **[Hyperband](../automl/hyperparameter-tuning/Hyperband.md)** — early-stopping bandit method, great for many quick candidates.
- Automated tools wrap all of this: see [auto-sklearn](../automl/auto-sklearn.md), [h2o-automl](../automl/h2o-automl.md).

## Practical advice

- Log *everything* (LR, batch size, seed) or results are unreadable.
- Tune LR first, then architecture, then regularization.
- Use a fixed validation set for search and a held-out test set for final check — searching on the test set is a form of overfitting.
