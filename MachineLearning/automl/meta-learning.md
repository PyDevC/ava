---
id: meta-learning
aliases: []
tags: []
---

# Meta-learning

Learning to learn

There are three common approaches:
- using (cyclic) networks with external or internal memory (model-based)
- learning effective distance metrics (metrics-based)
- explicitly optimizing model parameters for fast learning (optimization-based).

The point of meta-learning: instead of training a model for one task, train a *learner* that can learn new tasks fast — the goal is generalization *across tasks* rather than within one task.

## The three families (in more detail)

1. **Model-based**: the network itself stores experience (e.g. a memory-augmented NN can write support examples to memory and read them at inference). Fast, but harder to scale.
2. **Metric-based**: learn an embedding where similar classes are close and different ones far apart; classify new tasks by distance (prototypical / matching / Siamese networks). See [metric-learning](metric-learning.md) and [few-shot-learning](few-shot-learning.md).
3. **Optimization-based**: learn the *optimizer* — e.g. **MAML** finds initial weights that adapt to any new task in 1–2 gradient steps. See [model-agnostic-meta-learning](model-agnostic-meta-learning.md).

## Training recipe (episodic)

- Sample a batch of tasks (each with support + query split).
- For each task: adapt quickly on the support set, compute loss on the query set.
- Update the *meta-parameters* (embeddings / init-weights / memory) using the aggregated query losses.

## Why it matters

- The basis of [few-shot-learning](few-shot-learning.md) (5-way 1-shot image classification).
- Motivates modern "in-context learning" — transformers learning from examples in the prompt are arguably a form of meta-learning.
- Combined with [transfer-learning](../algorithms/transfer-learning.md): transfer = reuse weights; meta = learn *how to adapt* weights.
