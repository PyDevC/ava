# Linear function

A linear function maps input to output **proportionally, through the origin**:

```
f(x) = a·x
```

- `a` = slope / rate of change.
- Key property: `f(x+y) = f(x) + f(y)` and `f(c·x) = c·f(x)` (additivity + homogeneity). There's **no bias term**.

Compare [affine-function](affine-function.md): affine is `a·x + b` — linear is the special case with `b = 0`.

## In more dimensions

`f(x) = W·x` — a matrix times a vector. Linear maps are fully described by how they transform basis vectors, and they preserve the origin.

## Why it matters in ML

- **Linear models** assume a linear (no bias → through origin, though in practice regression models are affine) relationship: `ŷ = w·x`. See [tabular-regression](../algorithms/tabular-regression.md).
- **Linear algebra everywhere**: convolutions (a linear operator!), matrix multiplications in NNs, PCA/SVD.
- **The limitation**: composition of linear maps is linear — so deep linear networks are equivalent to a single linear map. Non-linearities (activations) are what unlock the expressive power described by [Universal-approximation-function](../algorithms/Universal-approximation-function.md).
- A linear classifier's decision boundary is a hyperplane: see [classification](../algorithms/classification.md).

## Related

- [affine-function](affine-function.md) — the +b generalization, which is what NN layers actually use.
- [lipsschitz-function](lipsschitz-function.md) — every linear map with bounded slope is Lipschitz (Lipschitz constant = operator norm).
- [logistic-function](logistic-function.md) — what you get when you push a linear score through a squashing non-linearity.
