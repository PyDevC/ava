# Backpropagation

Backprop is **the chain rule applied to a computation graph** — the algorithm that computes all weight gradients in a network in one forward pass + one backward pass, no matter how deep.

## The insight

The loss is a function of the weights through a long chain of layers. The chain rule says the gradient of the loss w.r.t. an earlier layer's weights factors through the later layers. Instead of computing each gradient independently (exponential work), backprop computes the *shared* intermediate — the gradient of the loss w.r.t. each layer's *output* — once, and reuses it going backward.

## Forward vs backward pass

```
Forward:   x → h₁ → h₂ → ... → y → L
Backward:  ∂L/∂y → ∂L/∂h₂ → ∂L/∂h₁ → ∂L/∂W₂, ∂L/∂W₁
```

Each backward step uses the chain rule:

```
∂L/∂hᵢ₋₁ = ∂L/∂hᵢ · ∂hᵢ/∂hᵢ₋₁
∂L/∂Wᵢ  = ∂L/∂hᵢ · ∂hᵢ/∂Wᵢ
```

The backward pass **reuses the activations saved during forward** — which is why PyTorch keeps intermediate tensors in memory until `backward()` (see [[../../PyTorch/PLAN]] autograd internals).

## The two failure modes

- **Vanishing gradients**: with sigmoid/tanh, each `∂hᵢ/∂hᵢ₋₁` shrinks (derivative ≤ 1/4 for sigmoid), so the product across depth → 0. This is why [[../mathematical-function/relu-function]] replaced them and why **residual connections** (`x + F(x)`, gradient bypass = 1) work.
- **Exploding gradients**: the product grows unboundedly (RNNs, deep nets). Fix: **gradient clipping** (cap `‖∇‖`), careful init (He/Xavier), or LayerNorm.

## What needs to be true for backprop

- The loss must be differentiable (see [[../algorithms/loss-function]]) — this is why we don't train on accuracy directly.
- The graph must be a DAG of differentiable ops — that's exactly the "graph" Dynamo/autograd builds (see [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]] for what breaks it).
- In PyTorch: `loss.backward()` walks the autograd graph, then the optimizer does `w -= η·g` (see [[model-optimization]]).

## Related

- [[model-optimization]] — the optimizer consumes the gradients backprop produces.
- [[../mathematical-function/gradient-hessian]] — the math behind it.
- [[../mathematical-function/relu-function]] — the activation that keeps backprop alive in deep nets.
- [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]] — what happens when backprop can't trace.
