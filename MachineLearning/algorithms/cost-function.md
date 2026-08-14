# Cost function

The cost function (a.k.a. objective / loss averaged over the whole dataset) measures **how wrong the model is**, aggregated across all training samples. Training = minimizing the cost.

## Cost vs loss

- **Loss**: error for a *single* sample, `L(y, ŷ)`.
- **Cost**: average (or sum) of losses over the dataset, e.g. mean squared error: `J = (1/m)·Σ L(y_i, ŷ_i)`.

Often the terms are used interchangeably; "cost function" usually implies the aggregate. See [loss-function](loss-function.md) for the per-sample side.

## Why the choice matters

The cost function defines the objective, so it decides:

- What the model optimizes (correctness? calibrated probabilities? robustness?).
- Differentiability — gradient descent needs smooth costs, which is why we rarely train on accuracy directly (it's piecewise constant).
- Sensitivity to outliers (L2 / MSE punishes big errors a lot; L1 / MAE less).

## Examples

- Regression: MSE, MAE, Huber.
- Classification: cross-entropy / log loss.
- Regularized objectives add a penalty term, e.g. `J = MSE + λ·Σw²` (see [weighted-linear-regression](weighted-linear-regression.md) for weighted variants).

## Related

- [cost](cost-function.md) vs [loss](loss-function.md) — pick the right one for the task.
- The weighted version in [weighted-linear-regression](weighted-linear-regression.md) changes the cost to `Σ w_i·(y_i - ŷ_i)²`.
