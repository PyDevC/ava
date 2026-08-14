# tanh function

tanh (hyperbolic tangent) squashes any real number into `(-1, 1)`:

```
tanh(x) = (e^x - e^(-x)) / (e^x + e^(-x)) = 2σ(2x) - 1
```

where `σ` is the [logistic-function](logistic-function.md). It's the sigmoid *centered at 0*: same S-shape, but symmetric — output range (-1,1) instead of (0,1).

## Properties

- `tanh(0) = 0`, derivative `tanh'(x) = 1 - tanh²(x)` (max 1 at 0).
- Zero-centered output means the next layer receives positive *and* negative inputs — usually trains faster than sigmoid (whose outputs are all positive, causing biased gradients).
- Still saturates for large |x| → vanishing gradient at the extremes (see [logistic-function](logistic-function.md)'s same caveat).

## Where it's used

- **RNN/LSTM gates**: `tanh` for the candidate cell state / hidden state, sigmoid for the gates (see [deeplearning](../deeplearning/PLAN.md) LSTM note).
- **Classic activation** (pre-ReLU era); replaced by [relu-function](relu-function.md) for deep feed-forward nets.
- Numerical tricks: `tanh` is used in approximations (e.g. GELU's tanh approximation, see [relu-function](relu-function.md)).

## Related

- [logistic-function](logistic-function.md) — its shifted/scaled twin.
- [relu-function](relu-function.md) — the modern default it lost to.
- [lipsschitz-function](lipsschitz-function.md) — tanh is 1-Lipschitz.
