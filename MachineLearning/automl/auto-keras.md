---
id: auto-keras
aliases: []
tags: []
---

# Auto Keras

Auto-Keras (paper: "Auto-Keras: An Efficient Neural Architecture Search System") is a NAS tool for deep learning, built on Keras, that makes the search **efficient** rather than brute-force.

## Efficient architecture search

The key idea is **network morphism**: changing a neural architecture while *preserving the functionality* of the current network. Because each morph (widening, deepening, adding a skip connection) keeps what's already learned, the search can warm-start from previously trained weights instead of retraining from scratch at every step — a huge saving vs [NAS](NAS.md)'s naive train-every-candidate approach.

## The search problem

Formally, given a search space `F`, data split into `D_train`/`D_val`, and a cost function, the goal is:

```
f*  = argmin_{f∈F} Cost(f(θ*), D_val)
θ*  = argmin_θ     L(f(θ),  D_train)
```

The inner problem finds the best weights for a given architecture; the outer problem searches over architectures. Auto-Keras' contribution is making the *outer* loop cheap via network morphism + Bayesian optimization over the morph space (it uses a graph neural network to model which morphs are promising).

## Related

- [NAS](NAS.md) — the general architecture-search problem.
- [model-agnostic-meta-learning](model-agnostic-meta-learning.md) — another "search/warm-start" family (meta-learning).
- [automl-comparison](automl-comparison.md) — the tool landscape.
