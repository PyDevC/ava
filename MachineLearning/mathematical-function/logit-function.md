# Logit function

The logit is the **inverse of the [logistic-function](logistic-function.md)** — it maps a probability `p ∈ (0,1)` to a real number:

```
logit(p) = ln( p / (1-p) )
```

- `p = 0.5` → 0; `p → 1` → +∞; `p → 0` → -∞.
- It converts a probability into an unbounded "log-odds" score.

## Why it matters in ML

- **The link function of logistic regression**: `logit(P(y=1|x)) = w·x + b`. The model is *linear in the log-odds space* — that's why logistic regression is "linear". Fitting it estimates `w, b`, then `σ` turns the score back into a probability.
- **Odds interpretation**: `p/(1-p)` is the odds; log-odds are linear in the features. `e^w` = multiplicative change in odds per unit feature.
- **Log-space numerics**: computing with logits instead of probabilities is numerically stable (no underflow to 0) — softmax implementations do exactly this (log-softmax).

## Notes

- Both logistic and logit appear because one is the inverse of the other: `σ(logit(p)) = p` and `logit(σ(x)) = x`.
- The multi-class analog is log-softmax (softmax's logit form) — see [softmax-function](softmax-function.md).
- Related to the information-theoretic view: `-ln(p)` is information/surprise; the logit mixes two log-probabilities.

## Related

- [logistic-function](logistic-function.md) — its inverse.
- [softmax-function](softmax-function.md) — the vector generalization.
- [affine-function](affine-function.md) — the `w·x + b` the logit is set equal to.
