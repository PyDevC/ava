# Normal (Gaussian) distribution

The normal distribution is the bell curve — the default assumption for continuous noise and continuous errors:

```
f(x; μ, σ²) = (1/√(2πσ²)) · exp(-(x-μ)² / (2σ²))
```

- `μ` = mean (center), `σ` = standard deviation (spread), `σ²` = variance.
- **Empirical rule**: ~68% of mass within ±1σ, ~95% within ±2σ, ~99.7% within ±3σ.

## Why it's everywhere in ML

- **The noise model**: least-squares regression *is* maximum likelihood under Gaussian errors — see [weighted-linear-regression](../algorithms/weighted-linear-regression.md). That's why squared error is the "natural" regression loss.
- **Central Limit Theorem**: sums of many independent things → normal. Averages, sampling means, and error residuals all end up normal-ish, which justifies z-scores, confidence intervals, and t-tests.
- **Weight initialization**: the classic init is `N(0, 1/√fan_in)` (Xavier/He) — keeps activations' variance stable across layers (see [deeplearning](../deeplearning/PLAN.md)).
- **Gaussian Mixture Models**: k-means is the hard-assignment special case (see [k-means](../algorithms/k-means.md)).
- **Bayesian ML**: Gaussian priors = L2/ridge regularization; the posterior is also Gaussian (conjugate).

## The standard normal and the CDF

The **standard normal** `Z ~ N(0,1)` is the reference; `Φ(z)` is its CDF. The [quantile-function](quantile-function.md) `Φ⁻¹(p)` gives z-scores (e.g. `Φ⁻¹(0.975) ≈ 1.96` for 95% confidence). GELU uses `x·Φ(x)` (see [relu-function](relu-function.md)).

## Related

- [quantile-function](quantile-function.md) — inverse CDF = z-scores.
- [regularization](../algorithms/regularization.md) — Gaussian prior → L2 penalty.
- [bias-variance-tradeoff](../algorithms/bias-variance-tradeoff.md) — irreducible noise is usually modeled as normal.
- [indicator-function](indicator-function.md) — uniform densities use it; normal is the other canonical continuous density.
