# Logistic function

The logistic (sigmoid) function squashes any real number into `(0, 1)`:

```
σ(x) = 1 / (1 + e^(-x))
```

- At `x = 0` → 0.5. As `x → +∞` → 1, as `x → -∞` → 0.
- Smooth, monotonic, differentiable everywhere — and its derivative is beautifully simple: `σ'(x) = σ(x)·(1 - σ(x))`.

## Why it matters in ML

- **Binary classification**: logistic regression pushes a linear score `w·x + b` through `σ` to get a probability `P(y=1 | x)`, trained with [[MachineLearning/algorithms/loss-function|log loss]]. See [[MachineLearning/algorithms/classification]].
- **Neural network activation**: the classic "squashing" non-linearity (see [[MachineLearning/algorithms/neural-network]]). Modern nets prefer ReLU (avoids vanishing gradients), but sigmoid is still used in gates (LSTM, GRU) and final probability outputs.
- **It is the inverse of the logit** — see [[logit-function]].
- Smooth version of the [[indicator-function]]/step (a 0→1 jump, relaxed to be differentiable), making it Lipschitz and gradient-friendly (see [[lipsschitz-function]]).

## Notes

- Problem: **saturation** — for large |x| the gradient → 0 (vanishing gradient).
- The multi-class generalization is the [[softmax-function]].
- The affine input inside `σ` — see [[affine-function]].

## Related

- [[logit-function]] — its inverse: `logit(p) = ln(p/(1-p))`.
- [[softmax-function]] — the K-class version.
- [[linear-function]] → logistic regression = linear model + logistic link.
