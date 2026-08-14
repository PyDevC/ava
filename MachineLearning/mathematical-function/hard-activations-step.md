# Hard sigmoid / hard tanh and the step function

These are **piecewise-linear relaxations** of the smooth activations — cheap to compute, and friendly to quantized/edge inference.

## Hard sigmoid

```
H-sigmoid(x) = clamp((x + 3)/6, 0, 1)
```

A linear ramp that saturates at 0 and 1 (instead of the [logistic-function](logistic-function.md)'s exponential curve). Cost: one add, one scale, one clamp — no `exp`. Common in MobileNet-style architectures (MobileNetV3 uses it).

## Hard tanh

```
H-tanh(x) = clamp(x, -1, 1)
```

The "clipped linear" version of [tanh-function](tanh-function.md) — just a clamp. Extremely cheap; good enough where saturation is all that matters.

## Heaviside / step

```
step(x) = 1 if x ≥ 0, else 0
```

The *hardest* of the hard functions — a literal jump (the [indicator-function](indicator-function.md) at x ≥ 0). No gradient (0 almost everywhere), so it can't be trained directly — this is why activations must be continuous (see [Universal-approximation-function](../algorithms/Universal-approximation-function.md) and the loss-differentiability rule in [loss-function](../algorithms/loss-function.md)). It lives in the *inference/symbolic* world: binarized networks, decision thresholds, gating logic.

## Why "hard" versions matter

- **Quantization**: after weight/activation quantization, exact `exp`/`tanh` are expensive; clamps are free (see [model-optimization](../deeplearning/model-optimization.md) inference notes).
- **FPGA/edge inference**: no transcendental instructions; piecewise-linear is the only option.
- **Training with hard functions**: either train the smooth version and swap at inference, or use **straight-through estimation** (treat the hard op as identity in backward) — the standard trick for binarized networks.

## Related

- [logistic-function](logistic-function.md) / [tanh-function](tanh-function.md) — the smooth originals.
- [indicator-function](indicator-function.md) — Heaviside's "≥ 0" form.
- [relu-function](relu-function.md) — hard tanh is literally ReLU shifted/clipped.
- [lipsschitz-function](lipsschitz-function.md) — all these hard versions are 1-Lipschitz.
