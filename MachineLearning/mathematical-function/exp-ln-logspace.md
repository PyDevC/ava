# exp / ln in ML (log-space numerics)

ML works in **log space** almost everywhere — not because it's elegant, but because it's numerically necessary and analytically convenient. Two identities rule:

```
ln(a·b) = ln a + ln b
ln(a^b) = b · ln a
```

Multiplication and powers become addition and multiplication — and tiny probabilities become *sums of moderate negative numbers* instead of underflowing products.

## Why log space

- **Underflow**: `softmax([1000, 1001, 999])` computes `e^1000 ≈ inf/underflow`. Subtract the max first (shift invariance, see [softmax-function](softmax-function.md)), or use **log-softmax** — the log form never leaves moderate range.
- **The cross-entropy trick**: `-ln σ(z)` = `softplus(-z)` — a single numerically stable function instead of "compute probability, then log it". Every loss library implements the fused form.
- **Products of probabilities**: `P(x₁..x_n) = Π P(xᵢ)` underflow for n > 100. The **log-likelihood** `Σ ln P(xᵢ)` is the standard objective (see [loss-function](../algorithms/loss-function.md)).
- **KL/entropy**: `p·ln(p/q)` — information theory is log-based (see the surprise note in [logit-function](logit-function.md)).

## Key stable functions

- **LogSumExp**: `ln(Σ e^zᵢ)` computed as `c + ln(Σ e^(zᵢ-c))` with `c = max zᵢ`. The parent of log-softmax.
- **Softplus**: `ln(1 + e^x)` ≈ `max(0, x)` for large |x| — smooth ReLU (see [relu-function](relu-function.md)), numerically stable at the extremes.
- **`stable_softmax` = z - LogSumExp(z)`: the log-softmax, what you actually compute before cross-entropy.

## The logit/odds link

The [logit-function](logit-function.md) is itself a log ratio — `ln(p/(1-p))` — and logistic regression is *linear in log-odds* (see [logistic-regression](../algorithms/logistic-regression.md)). The whole model runs in log space by design.

## Related

- [logistic-function](logistic-function.md) / [logit-function](logit-function.md) — log-based links.
- [softmax-function](softmax-function.md) — LogSumExp is its stable form.
- [naive-bayes](../algorithms/naive-bayes.md) — the canonical log-probability product.
- [gradient-hessian](gradient-hessian.md) — derivatives of these functions are what backprop computes.
