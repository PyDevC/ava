# Logistic function

The logistic (sigmoid) function squashes any real number into `(0, 1)`:

```
σ(x) = 1 / (1 + e^(-x))
```

- At `x = 0` → 0.5. As `x → +∞` → 1, as `x → -∞` → 0.
- Smooth, monotonic, differentiable everywhere — and its derivative is beautifully simple: `σ'(x) = σ(x)·(1 - σ(x))`.

## Why it matters in ML

- **Binary classification**: logistic regression pushes a linear score `w·x + b` through `σ` to get a probability `P(y=1 | x)`, trained with [log loss](../algorithms/loss-function.md). See [classification](../algorithms/classification.md).
- **Neural network activation**: the classic "squashing" non-linearity (see [neural-network](../algorithms/neural-network.md)). Modern nets prefer ReLU (avoids vanishing gradients), but sigmoid is still used in gates (LSTM, GRU) and final probability outputs.
- **It is the inverse of the logit** — see [logit-function](logit-function.md).
- Smooth version of the [indicator-function](indicator-function.md)/step (a 0→1 jump, relaxed to be differentiable), making it Lipschitz and gradient-friendly (see [lipschitz-function](lipschitz-function.md)).

## Notes

- Problem: **saturation** — for large |x| the gradient → 0 (vanishing gradient).
- The multi-class generalization is the [softmax-function](softmax-function.md).
- The affine input inside `σ` — see [affine-function](affine-function.md).

## Related

- [logit-function](logit-function.md) — its inverse: `logit(p) = ln(p/(1-p))`.
- [softmax-function](softmax-function.md) — the K-class version.
- [linear-function](linear-function.md) → logistic regression = linear model + logistic link.
