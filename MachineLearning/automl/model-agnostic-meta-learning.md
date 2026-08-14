# Model-agnostic meta-learning (MAML)

MAML ("Model-Agnostic Meta-Learning", Finn et al. 2017) is the flagship **optimization-based** approach in [meta-learning](meta-learning.md). The name says it all: it works with any model trained by gradient descent.

## The core idea

Find initial model parameters `θ` such that **one (or a few) gradient steps** on a new task's support set gives a good solution for that task. We meta-learn *the initialization*, not the final task model:

```
for each task τᵢ sampled in a batch:
    θᵢ' = θ - α·∇_θ L_τᵢ(θ)          # inner loop: adapt on support set
    loss(θᵢ') on query set
minimize θ:  Σᵢ L_τᵢ(θᵢ')           # outer loop: meta-gradient through the inner steps
```

The meta-gradient `∂L(θᵢ')/∂θ` flows **through the inner optimization** (backprop through the gradient steps), using second-order derivatives (first-order approximation FOMAML drops those to save memory).

## Why it works

A good `θ` lives near a point where the loss landscape is "flat and steerable" — small gradient steps on any task in the family move you into that task's basin. The meta-learning picks out the shared structure of all tasks.

## Properties

- **Task-agnostic**: works for few-shot classification ([few-shot-learning](few-shot-learning.md)), regression, RL.
- Needs no special architecture — any differentiable model.
- Costly: each meta-step unrolls several inner gradient steps (memory/compute heavy).

## Related

- [meta-learning](meta-learning.md) — umbrella concept.
- [few-shot-learning](few-shot-learning.md) — the canonical application.
- [transfer-learning](../algorithms/transfer-learning.md) — contrast: transfer reuses a trained model; MAML learns how to adapt.
