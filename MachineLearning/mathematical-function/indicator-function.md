# Indicator function

The indicator function returns **1 if a condition holds, 0 otherwise**:

```
1_A(x) = 1  if x ∈ A
         0  otherwise
```

It's the mathematical way to write "yes/no" membership.

## Uses in ML

- **Losses**: 0–1 loss `1_{ŷ ≠ y}` counts misclassifications (the "accuracy" objective — not differentiable, which is why we optimize surrogates like [[MachineLearning/algorithms/loss-function|log loss]]).
- **Probability / density**: the indicator of the support appears in density functions (e.g. uniform distribution: `1_{a≤x≤b}/(b-a)`).
- **Objective functions / constraints**: `1_{x is feasible}` inside constrained optimization.
- **Masking**: in [[MachineLearning/algorithms/injectivity|injective]] encodings and sampling, indicators define valid regions.
- **Counting / expectations**: `E[1_A] = P(A)` — expectations of indicators are probabilities.

## Related

- It's the *step* function at 0: `1_{x>0}` jumps from 0 to 1 at x=0.
- Compare the continuous "soft" cousins used in ML to stay differentiable: the [[logistic-function]] (smooth 0→1) and the [[softmax-function]] (smooth one-hot).
- The [[quantile-function]] and empirical CDFs are built from indicators: `F̂(x) = (1/n) Σ 1_{x_i ≤ x}`.
