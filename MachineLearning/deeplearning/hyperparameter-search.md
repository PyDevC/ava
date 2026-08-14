# Hyperparameter Search

Hyperparameters are the options in a model that can be tuned to get the best performance with best efficiency out of the model.

Choosing the Hyperparameters for the model can be challenging and choosing a close enough hyperparameter comes with experience.

## What is a hyperparameter (vs a parameter)

- **Parameters**: learned from data during training (weights `w`, biases).
- **Hyperparameters**: set *before* training — learning rate, batch size, number of layers/units, optimizer choice, dropout, weight decay... They control the learning process, and you can't get them from the data directly (see [[MachineLearning/automl/hyperparameter-tuning]]).

## The important ones for deep learning

- **Learning rate** — the single most impactful one. Too high → diverges, too low → slow. Needs a [[how-to-use-lr-scheduler]] to decay over time.
- **Batch size** — interacts with LR (larger batches usually want larger LR); affects memory and convergence.
- **Optimizer choice + its params** (momentum, Adam betas, weight decay).
- **Architecture**: depth, width, dropout, activation.
- **Regularization**: dropout rate, weight decay, augmentation.

## How to search

- **Manual / experience-based** — decent starting points from prior knowledge, then iterate. Still the practical norm.
- **[[MachineLearning/automl/hyperparameter-tuning/grid-search]] / [[MachineLearning/automl/hyperparameter-tuning/random-search]]** — brute force baselines; random usually beats grid in high dims.
- **[[MachineLearning/automl/hyperparameter-tuning/Bayesian-optimization]]** — sample-efficient, the smart choice for expensive runs.
- **[[MachineLearning/automl/hyperparameter-tuning/Hyperband]]** — early-stopping bandit method, great for many quick candidates.
- Automated tools wrap all of this: see [[MachineLearning/automl/auto-sklearn]], [[MachineLearning/automl/h2o-automl]].

## Practical advice

- Log *everything* (LR, batch size, seed) or results are unreadable.
- Tune LR first, then architecture, then regularization.
- Use a fixed validation set for search and a held-out test set for final check — searching on the test set is a form of overfitting.
