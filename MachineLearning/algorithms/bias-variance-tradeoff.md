# Bias-variance tradeoff

The bias-variance tradeoff explains why models fail the way they do, and why every family has a "sweet spot" for complexity. Test error of a model decomposes into three parts:

```
Expected test error = Bias² + Variance + Irreducible noise
```

- **Bias** — how far the *average* prediction is from the truth. High bias = the model can't even represent the pattern (underfitting).
- **Variance** — how much the prediction *fluctuates* between different training sets. High variance = the model is very sensitive to the specific data it saw (overfitting).
- **Irreducible noise** — the part no model can remove (label noise, missing features).

## The tradeoff curve

As model complexity grows:

- Bias ↓ (model can express the true pattern)
- Variance ↑ (more knobs = more sensitivity to training data)
- Total error is **U-shaped** — there's a complexity sweet spot.

This is exactly the intuition behind `learning_curve` plots and why a random forest of deep trees can still overfit while a depth-1 stump underfits. Note the count isn't the culprit: adding more trees to a forest *reduces* variance (averaging), so overfitting comes from the individual trees being too deep — forest depth is the complexity dial, tree count is not.

## The two failure modes

- **Underfitting (high bias)**: training error is also high. Fix: more capacity, better features, less regularization.
- **Overfitting (high variance)**: training error low, test error high. Fix: [regularization](regularization.md), more data, simpler model, cross-validation (see [cross-validation](cross-validation.md)).

## Practical meaning

- More data *reduces variance* without increasing bias — the cleanest lever.
- The whole point of [regularization](regularization.md), bagging (variance ↓ via averaging), and boosting with small trees (bias ↓) is to move along this curve deliberately.
- Deep learning's tricks (dropout, weight decay, augmentation) are all variance-reduction.

## Related

- [regularization](regularization.md) — the tool to trade bias for variance explicitly.
- [cross-validation](cross-validation.md) — how you find the sweet spot without looking at test data.
- [decision-trees](decision-trees.md) / [gradient-boosting](gradient-boosting.md) — concrete places the tradeoff shows up.
