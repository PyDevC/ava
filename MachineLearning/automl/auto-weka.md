# Auto-WEKA

Auto-WEKA is the AutoML tool built on top of **WEKA**, the classic Java machine learning workbench. It did for WEKA what [auto-sklearn](auto-sklearn.md) did for sklearn: automated selection of learning algorithm + hyperparameters.

## How it works

- Uses **Bayesian optimization (SMAC)** over a combined space: the choice of algorithm (34+ classifiers) × each algorithm's hyperparameters.
- The joint configuration space is hierarchical — the hyperparameters of non-selected algorithms are irrelevant, so SMAC handles this "conditional" structure.
- Evaluates with internal cross-validation.

## Why it matters historically

Auto-WEKA (2013) was one of the first practical demonstrations that *algorithm selection + hyperparameter tuning* as one unified black-box optimization problem works and beats hand-tuning and even some expert choices. It's the direct ancestor of the modern [hyperparameter-tuning](hyperparameter-tuning.md) + AutoML toolchains.

## Current relevance

WEKA/Auto-WEKA is less common in modern production stacks (Python tools dominate), but the *ideas* it established — combined algorithm/hyperparameter search spaces, conditional params, Bayesian optimization — are exactly what [auto-sklearn](auto-sklearn.md), [mlbox](mlbox.md) and [h2o-automl](h2o-automl.md) implement today.
