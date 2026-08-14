# Convexity and Jensen's inequality

Convexity is why optimization is *well-behaved*: for a convex function, **every local minimum is a global minimum**, and gradient descent is guaranteed to converge to it.

## Convex definition

`f` is convex on its domain if the chord lies above the curve:

```
f(θx + (1-θ)y) ≤ θf(x) + (1-θ)f(y)   for all 0 ≤ θ ≤ 1
```

Equivalently (smooth case): `f(y) ≥ f(x) + ∇f(x)·(y-x)` — the tangent is always a lower bound — or the Hessian `H ⪰ 0` (see [[gradient-hessian]]).

## Which ML objectives are convex

- **Linear/logistic regression** losses (see [[../algorithms/logistic-regression]]) — convex in the weights. This is why they converge from any start.
- **SVMs** (hinge + regularization) — convex (see [[../algorithms/SVM]]).
- **Ridge/lasso** objectives — convex (see [[../algorithms/regularization]]).
- **Neural nets** — *not* convex (many local minima/saddles). The whole discipline of optimizers, momentum, and LR schedules (see [[../deeplearning/model-optimization]]) exists because of this.

## Jensen's inequality

For convex `f` and a distribution over `x`:

```
f(E[x]) ≤ E[f(x)]
```

- The bridge between expectations and functions — and the root of lots of ML math.
- **Why it matters**: it powers the **ELBO**/variational inference (`log` is concave, so `log E[·] ≥ E[log ·]` — the evidence lower bound), and is the basis for the "E/M" thinking behind k-means/EM (see [[../algorithms/k-means]]).

## Related

- [[gradient-hessian]] — the Hessian test for convexity.
- [[../algorithms/logistic-regression]] — a convex objective in practice.
- [[../deeplearning/model-optimization]] — the non-convex world where the tools differ.
