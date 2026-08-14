# Gradient and Hessian

These are the calculus tools of all of ML — optimization *is* following the gradient.

## Gradient (vector of first derivatives)

For `f: R^n → R`, the gradient `∇f(x)` is the vector of partial derivatives:

```
∇f(x) = ( ∂f/∂x₁, ∂f/∂x₂, ..., ∂f/∂x_n )
```

- Points in the **direction of steepest ascent**; `-∇f` is steepest descent.
- Gradient descent: `x ← x - η·∇f(x)` — the update rule of every optimizer (see [model-optimization](../deeplearning/model-optimization.md)).
- The gradient is a *function of x* — recompute it at every step (that's what `backward()` does; see backprop in [deeplearning](../deeplearning/)).

## Hessian (matrix of second derivatives)

`H(x)` is the matrix of second partials, `H_ij = ∂²f/∂xᵢ∂xⱼ`.

- Tells you the **curvature** of the loss: convex iff H is positive semi-definite everywhere.
- Newton's method uses `H⁻¹∇f` to jump directly to the optimum (but H is n² — infeasible for modern models; hence Adam's diagonal approximation).
- **Second-order information is why XGBoost converges fast** — it uses both `g` (gradient) and `h` (Hessian) per leaf (see [XGBoost](../algorithms/XGBoost.md)).
- XGBoost's split-gain formula `gain = g²/(h+λ)` is literally second-order Taylor of the loss.

## The chain rule connection (backprop)

Backprop is just the chain rule applied cleverly: the gradient of the loss w.r.t. a weight = product of per-layer Jacobians. Keeping that derivative flowing through a deep net is exactly the vanishing-gradient story that [relu-function](relu-function.md) fixes.

## Practical notes

- Gradients are noisy (computed on batches) — momentum/Adam average them (see [model-optimization](../deeplearning/model-optimization.md)).
- `∇f` near zero = local optimum or saddle; Hessian distinguishes them (saddle: some positive, some negative eigenvalues).
- Gradient *clipping* bounds `‖∇f‖` to prevent exploding gradients (see the L2 norm note in [norms](norms.md)).

## Related

- [norms](norms.md) — the L2 norm of the gradient is what we bound/clip.
- [loss-function](../algorithms/loss-function.md) — what we're differentiating.
- [Universal-approximation-function](../algorithms/Universal-approximation-function.md) — the functions whose gradients we compute.
