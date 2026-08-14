# Weighted linear regression

Weighted linear regression fits:

```
ŷ = Xw        minimizing   Σ_i w_i·(y_i - ŷ_i)²
```

Each sample's contribution to the [cost-function](cost-function.md) is scaled by a per-sample weight. Closed-form solution:

```
w* = (Xᵀ W X)⁻¹ Xᵀ W y
```

where `W = diag(w_i)`. It's a one-line change from ordinary least squares: `XᵀX` → `XᵀWX`.

## Why weight samples

- **Heteroscedastic errors**: samples with higher measurement noise get *lower* weight (like weighted least squares / GLS).
- **Importance / frequency**: rare but important groups matter more; or correct for sampling bias (inverse propensity weighting).
- **Time decay**: recent data weighted higher (forgetting factor).
- **Robustness**: down-weight outliers (iteratively reweighted least squares, IRLS) — which is exactly what robust estimators like Huber do.

## Interpretation

Weighted regression is equivalent to *duplicating* each sample `w_i` times, or to scaling each row by `√w_i` and running OLS. It's also the building block of boosting's view on data reweighting (AdaBoost literally reweights samples each round).

## Related

- [tabular-regression](tabular-regression.md) — the general setting this lives in.
- [loss-function](loss-function.md) — weighting changes the loss each sample contributes.
- Weighted MSE is a plain linear model with a weighted cost — same closed form, so it's cheap to experiment with.
