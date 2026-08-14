# Prototypical and matching networks

The metric-based implementations of [few-shot-learning](few-shot-learning.md): instead of fine-tuning a model per task, **classify by distance in an embedding space**. Given a small support set, compute class prototypes and compare new queries to them — no gradient updates at inference time.

## Prototypical networks (Snell 2017)

- For each class in the support set, compute its **prototype**: the mean embedding of its examples: `c_k = (1/|S_k|) Σ f_θ(x_i)`.
- Classify a query: nearest prototype in the embedding space (cosine / Euclidean):

```
p(y=k | x) = softmax(-d(f_θ(x), c_k))
```

- The loss is the negative log-probability of the true class over a batch of episodes. Simple, elegant, strong few-shot results — and the "prototype = mean embedding" is exactly the pattern used in modern retrieval/metric setups (see [metric-learning](metric-learning.md)).

## Matching networks (Vinyals 2016)

- The earlier form: attention over the support set instead of mean prototypes — `p(y|x) = Σ α(x, x_i) · y_i` where `α` is attention (see [seq2seq-attention](../nlp/seq2seq-attention.md)). "Matching" = comparing query to every support example via attention, then weighting their labels.
- Prototypical nets are the mean-based simplification; matching nets are the attention-based one. Both are **non-parametric at inference**: the "parameters" are the support examples themselves.

## The shared idea

- **Learning the metric, not the classifier**: the network's job is to learn an embedding where same-class examples cluster — exactly what contrastive/metric losses do (see [metric-learning](metric-learning.md), [embeddings-for-search](../nlp/embeddings-for-search.md)).
- **Episodic training**: train on random *episodes* (support + query drawn from disjoint classes) so the model learns the few-shot *procedure*, not a fixed task. This is the "learning to learn" frame (see [meta-learning](meta-learning.md)).

## Related

- [few-shot-learning](few-shot-learning.md) — the problem these solve.
- [metric-learning](metric-learning.md) — the embedding-distance machinery.
- [meta-learning](meta-learning.md) / [model-agnostic-meta-learning](model-agnostic-meta-learning.md) — the other (gradient-based) family.
- [embeddings-for-search](../nlp/embeddings-for-search.md) — the same distance idea at production scale.
