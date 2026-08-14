# Lipschitz function

A function `f` is **Lipschitz (continuous)** with constant `K` if its outputs can't change faster than `K` times its inputs:

```
|f(x) - f(y)| ≤ K · |x - y|    for all x, y
```

`K` is the Lipschitz constant — an upper bound on how steep the function can be anywhere.

## Why it matters in ML

- **Gradient stability / training**: if the model is Lipschitz with small `K`, gradients can't explode. Gradient clipping and spectral normalization both *enforce* Lipschitz constraints to stabilize GANs and RNNs.
- **Robustness**: a Lipschitz classifier with bounded `K` guarantees small input perturbations can only shift the output a little — this is the theory behind certified adversarial robustness.
- **Generalization bounds**: many learning-theory results are stated in terms of the Lipschitz constant of the hypothesis class.
- **Guarantees for ODE-based models**: neural ODEs / residual nets (an Euler discretization) rely on Lipschitz-ness for existence/uniqueness of solutions.

## Facts

- Every **linear** map is Lipschitz: `K = ‖W‖` (operator norm) — see [[linear-function]].
- Smooth activations like [[logistic-function]] and [[softmax-function]] are Lipschitz; ReLU is Lipschitz with K=1 (it just clips the slope).
- If a network uses only K-Lipschitz layers (e.g. L2-norm weights, GroupSort, lip-swish activations), the whole network is `K^depth`-Lipschitz.
- Rademacher's theorem: Lipschitz functions are differentiable almost everywhere — you can do calculus on them.

## Related

- [[logistic-function]] — a 1-Lipschitz (actually 1/4-slope-max) squashing function.
- [[linear-function]] / [[affine-function]] — the simplest Lipschitz maps.
