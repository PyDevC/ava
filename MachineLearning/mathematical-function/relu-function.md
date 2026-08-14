# ReLU and the activation family

ReLU (Rectified Linear Unit) is the default activation in modern networks:

```
ReLU(x) = max(0, x)
```

and its derivative is a step: 1 for `x > 0`, 0 for `x < 0` (undefined at exactly 0).

## Why ReLU won

- **Kills the vanishing gradient problem** (see [deeplearning](../deeplearning/) backprop notes): for positive inputs the gradient is exactly 1, so it doesn't shrink through layers (unlike [logistic-function](logistic-function.md)/tanh, which saturate to ~0 gradient).
- **Cheap**: a max, trivially parallel (unlike exp in sigmoid).
- **Sparse activations**: dead neurons output exactly 0, which also regularizes.

## The problems and the fixes

- **Dead ReLU**: a neuron with `x < 0` on all its inputs has gradient 0 *forever* and never recovers. Fixes:
  - **LeakyReLU**: `α·x` for `x < 0` (α ≈ 0.01) — small gradient keeps the neuron alive.
  - **Parametric ReLU (PReLU)**: α is *learned*.
- **Not differentiable at 0**: subgradient 0 is chosen by convention; fine in practice.

## Modern smooth variants

- **GELU**: `x·Φ(x)` — Gaussian-scaled ReLU, smooth everywhere, used by the Transformer family (BERT/GPT). ≈ `0.5x(1 + tanh(√(2/π)(x + 0.044715x³)))` approximation.
- **SiLU / Swish**: `x·σ(x)` — smooth, non-monotonic (dips below 0), often matches/beats ReLU at scale.
- **ELU**: exponential for negatives — smooth, keeps negatives non-dead.

The theme: smoothness (better gradients) vs computational cost (exp instead of max). ReLU is the practical default; GELU/SiLU win when compute allows (Transformers).

## Related

- [logistic-function](logistic-function.md) — the sigmoid these replaced; still used for gates/final probabilities.
- [lipsschitz-function](lipsschitz-function.md) — ReLU is 1-Lipschitz (slope bounded by 1).
- tanh — see the separate [tanh-function](tanh-function.md) note.
