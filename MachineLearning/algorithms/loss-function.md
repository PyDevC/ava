# Loss function

A loss function measures the **error of a single prediction** `L(y, ŷ)`. The aggregate over a dataset is the [[cost-function]]. Minimizing the loss is what training does.

## Common losses

- **Squared error** `(y - ŷ)²` — regression, differentiable, but sensitive to outliers.
- **Absolute error** `|y - ŷ|` — robust to outliers, not differentiable at 0.
- **Huber** — quadratic near 0, linear far away: best of both.
- **Cross-entropy / log loss** `-y·log ŷ` — classification (works with softmax/sigmoid outputs).
- **Hinge** `max(0, 1 - y·ŷ)` — SVM / max-margin.

## Choosing a loss

The loss must be:

- **Differentiable** (almost everywhere) if you use gradient descent — this is why we don't optimize accuracy directly (piecewise constant, gradient is 0).
- **Aligned with the real objective**: for imbalanced classes, plain cross-entropy may be wrong; weighted/focal losses exist for that.
- Matched to the output type: regression → continuous loss, probabilities → log loss, ranked lists → ranking loss (see [[ranking]]).

## In boosting

Boosting ([[Gradient-boosting]]) is defined *by* a loss: each tree fits the **gradient of the loss** w.r.t. predictions. Change the loss and you change the whole algorithm behavior (e.g. binomial deviance for classification, `reg:squarederror` for regression in [[XGBoost]]).

## Related

- [[cost-function]] — the average of losses over data.
- [[weighted-linear-regression]] — weights multiply each sample's loss.
