# Regularization

Regularization adds a **penalty on model complexity** to the objective, trading a bit of bias (see [bias-variance-tradeoff](bias-variance-tradeoff.md)) for a large reduction in variance. It's the main way to stop overfitting when you can't add data.

```
J_reg(w) = J(w) + λ · R(w)
```

`λ` controls how hard the penalty bites. As `λ → 0` you're back to the unregularized model; as `λ → ∞` the model collapses toward the simplest form.

## L2 / Ridge (the default)

```
R(w) = Σ wᵢ²
```

Pulls weights toward 0 but never exactly 0. Shrinks large weights the most (squaring punishes big values). The closed-form solution becomes `(XᵀX + λI)⁻¹Xᵀy` — the `λI` also fixes numerical instability when `XᵀX` is near-singular. This is what `weight_decay` in AdamW does (see [optimizers](../deeplearning/optimizers.md)).

## L1 / Lasso

```
R(w) = Σ |wᵢ|
```

Also pulls weights toward 0, but does it *exactly* — L1 gives **sparse** models (many weights become precisely 0). Use when you want feature selection. L1 is non-smooth at 0, so it needs subgradient or proximal methods (unlike L2's plain gradient).

## Elastic net

```
R(w) = λ₁·Σ|wᵢ| + λ₂·Σwᵢ²
```

Both penalties: L1 for sparsity, L2 for grouping correlated features. The practical choice when features are correlated.

## Beyond weight penalties

The same "complexity penalty" idea shows up everywhere:

- **Tree-based**: `max_depth`, `min_samples_leaf`, and the `γ`/`λ` terms in [XGBoost](XGBoost.md)'s objective.
- **Dropout** (NNs): randomly zero neurons — a form of implicit ensembling (see [deeplearning](../deeplearning/)).
- **Early stopping**: stop training when validation error stops improving — limits how far weights travel.
- **Data augmentation**: a "regularizer" on the data distribution itself.

## Practical rules

- **Standardize features first** — penalties are scale-sensitive (a big-valued feature looks "large").
- `λ` is a [hyperparameter-tuning](../automl/hyperparameter-tuning.md) target — cross-validate it (see [cross-validation](cross-validation.md)).
- Linear/regression: ridge when features are correlated; lasso when you want sparsity; elastic net when in doubt.

## Related

- [bias-variance-tradeoff](bias-variance-tradeoff.md) — why you regularize.
- [cost-function](cost-function.md) — where the penalty term attaches (`J + λR`).
- [cross-validation](cross-validation.md) — how you pick `λ`.
- [weighted-linear-regression](weighted-linear-regression.md) — the base model ridge/lasso modify.
