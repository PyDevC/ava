# Uniform distribution

The uniform distribution gives every value in a range equal probability — the "no information" prior.

```
X ~ U(a, b):  f(x) = 1/(b-a)   for a ≤ x ≤ b, else 0
```

The density is a constant (note the [indicator-function](indicator-function.md) writing it: `f(x) = 1_{a≤x≤b}/(b-a)`). Mean `(a+b)/2`, variance `(b-a)²/12`.

## Why it matters in ML

- **Sampling everything**: `U(0,1)` is the *source* of randomness. **Inverse-transform sampling**: draw `u ~ U(0,1)`, output `F⁻¹(u)` (the [quantile-function](quantile-function.md)) — gives samples from any distribution. GANs and many generative models start from uniform noise.
- **Initialization**: random uniform weight init (`U(-1/√fan, 1/√fan)`); modern He/Xavier use a similar scaling logic.
- **Dropout**: `mask ~ Bernoulli(p)` is literally `u < p` — the coin-flip implementation (see [regularization](../algorithms/regularization.md)).
- **Search**: uniform distributions define the ranges in [random-search](../automl/hyperparameter-tuning/random-search.md) and Bayesian optimization priors.
- **Regularization view**: an L2 penalty is equivalent to a Gaussian prior (see [regularization](../algorithms/regularization.md)); a *uniform* prior over a box is "no prior" — anything in range is equally likely.

## Related

- [quantile-function](quantile-function.md) — inverse transform sampling uses it.
- [bernoulli-categorical](bernoulli-categorical.md) — the discrete cousins.
- [indicator-function](indicator-function.md) — its density's support set.
- [normal-distribution](normal-distribution.md) — the other canonical continuous distribution.
