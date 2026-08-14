# Universal approximation function

The universal approximation theorems say that a **feed-forward neural network with a single hidden layer (with enough neurons) and a non-linear activation can approximate any continuous function on a compact domain arbitrarily well**.

## The statement (informally)

For any continuous `f: [0,1]^n → R` and any `ε > 0`, there exists a network:

```
ŷ = Σ_j v_j · σ(w_j·x + b_j)
```

(with `σ` a squashing activation like sigmoid/ReLU) such that `|ŷ(x) - f(x)| < ε` for all `x`.

## Why it's important

- It's the theoretical justification for why neural nets are a *universal function family* — powerful enough to represent anything we might want.
- It says **width** can do the job with a single hidden layer; but note it doesn't say how to *learn* it (that's the hard part), nor how many neurons you need (can be exponential), nor that deep networks aren't better.

## The subtlety nobody mentions

Universal approximation is about **existence + capacity**, not about learnability, generalization, or efficiency:

- Depth trades width: deep networks approximate some functions with exponentially fewer parameters than shallow ones.
- You still need data, a good [loss-function](loss-function.md), and an optimizer — see [neural-network](neural-network.md).
- Smoothness/complexity of the target determines how many units you really need.

## Related

- Activation functions like the [logistic-function](../mathematical-function/logistic-function.md) are the `σ` in the theorem.
- [injectivity](injectivity.md) matters for encoding/decoding architectures built on top of this idea.
