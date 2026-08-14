# Logistic regression

Logistic regression is the go-to **binary classifier**: it fits a linear decision boundary in *log-odds space* and outputs a probability via the [logistic-function](../mathematical-function/logistic-function.md). Despite the name it's a classifier, not a regression — the name is historical (it regresses on the log-odds).

## The model

```
p = P(y=1 | x) = σ(w·x + b) = 1 / (1 + e^-(w·x+b))
```

- `w·x + b` is a linear score — see [affine-function](../mathematical-function/affine-function.md).
- `σ` squashes it to (0,1) — see [logistic-function](../mathematical-function/logistic-function.md).
- Inverting: `logit(p) = ln(p/(1-p)) = w·x + b` — linear in log-odds, see [logit-function](../mathematical-function/logit-function.md).

## How it's trained

Minimize **cross-entropy / log loss** over the dataset:

```
J(w) = -1/m Σ [ yᵢ ln pᵢ + (1-yᵢ) ln(1-pᵢ) ]
```

This is the natural loss for probabilistic classification (see [loss-function](loss-function.md)). It's **convex** in `w` (unlike neural nets), so gradient descent is guaranteed to reach the optimum.

The gradient has a beautiful form — the same "residual" shape as linear regression:

```
∇J = 1/m Σ (pᵢ - yᵢ) xᵢ
```

Each step just moves the weights toward the misclassified examples, scaled by the prediction error.

## Why it's so useful

- Simple, fast, interpretable — you get *coefficients* you can read (`e^w` = multiplicative effect on odds).
- Gives **calibrated probabilities**, not just labels (see [classification](classification.md)).
- The multiclass extension is softmax regression — one `w_c` per class, softmax output (see [softmax-function](../mathematical-function/softmax-function.md)).
- With L2/L1 regularization (see [regularization](regularization.md)) it handles high-dimensional and correlated features.

## Limitations

- Linear boundary only — needs feature engineering or kernels for non-linear problems.
- Sensitive to feature scaling (unlike trees, see [descision-trees](descision-trees.md)).

## Related

- [classification](classification.md) — the task it solves.
- [weighted-linear-regression](weighted-linear-regression.md) — the same "residual gradient" story in the regression setting.
- [cost-function](cost-function.md) / [loss-function](loss-function.md) — where cross-entropy comes from.
