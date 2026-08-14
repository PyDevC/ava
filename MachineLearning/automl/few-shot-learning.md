# Few-shot learning

Few-shot learning = training a model to do a task with **only a handful of examples per class** (typically 1–5), as opposed to the thousands normal deep learning needs. It's the subfield of [meta-learning](meta-learning.md) closest to how humans learn.

## The setup

- **Support set**: the few labeled examples for the new task.
- **Query set**: examples to classify.
- **N-way K-shot**: N classes, K labeled examples each (e.g. 5-way 1-shot).

## The three big families (same split as meta-learning)

1. **Metric-based**: learn a distance function so that *similar classes are close together*. Then classify a query by nearest-neighbor against the support set.
   - Siamese networks (twin nets, contrastive loss), **prototypical networks** (class = mean embedding, "prototype"), matching networks, [metric-learning](metric-learning.md).
2. **Model-based**: external or internal memory stores training examples and reads them at inference (memory-augmented NNs, MANN).
3. **Optimization-based**: meta-learn how to take gradient steps so a few steps adapt quickly.
   - **MAML** — Model-Agnostic Meta-Learning: find init weights from which 1–2 gradient steps solve any new task (see [model-agnostic-meta-learning](model-agnostic-meta-learning.md)).

## Why it works at all

The trick is **episodic training**: you train on *many small tasks*, each shaped like the test condition (random N-way K-shot tasks from a base dataset). The model learns the *process* of learning from few examples, not a specific task.

## Related

- [meta-learning](meta-learning.md) — the umbrella ("learning to learn").
- [model-agnostic-meta-learning](model-agnostic-meta-learning.md) — the optimization-based approach.
- [metric-learning](metric-learning.md) — the distance-learning approach.
- [transfer-learning](../algorithms/transfer-learning.md) — different idea (one big pretraining, then adapt), but the same "little data" motivation.
