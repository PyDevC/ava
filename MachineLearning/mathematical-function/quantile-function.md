# Quantile function

The quantile function is the **inverse of the cumulative distribution function (CDF)**. Given a probability `p ∈ [0,1]`, it returns the value `x` below which a fraction `p` of the distribution lies:

```
Q(p) = inf { x : F(x) ≥ p }
```

- `Q(0.5)` = the **median**.
- `Q(0.25)` / `Q(0.75)` = quartiles.
- For a continuous distribution: `Q(p) = F⁻¹(p)`.

## Examples

- **Uniform [0,1]**: `Q(p) = p`.
- **Standard normal**: `Q(0.975) ≈ 1.96` (the z-score used in 95% confidence intervals).

## Why it matters in ML

- **Quantile regression**: predict specific quantiles (e.g. 10% and 90%) instead of just the mean — gives prediction intervals, robust to outliers. Loss = quantile (pinball) loss, a weighted [[MachineLearning/algorithms/loss-function]].
- **Inverse transform sampling**: sample `U ~ Uniform(0,1)`, output `Q(U)` → samples from any distribution. Core of GAN sampling and many generative methods.
- **Value at Risk / risk**: financial models report quantiles of loss distributions.
- **Normalization / feature engineering**: quantile transformers map data to a uniform distribution (better for some models than z-scaling).
- **Empirical CDF / quantiles**: sample quantiles from data (np.percentile) are the workhorse EDA/outlier tools.

## Related

- Built from the [[indicator-function]]: empirical CDF `F̂(x) = (1/n)Σ 1_{x_i ≤ x}`.
- The [[logistic-function]]/[[logit-function]] are quantile functions of specific distributions (logistic distribution).
- Quantile regression generalizes the median (a robust location) — see [[MachineLearning/algorithms/tabular-regression]].
