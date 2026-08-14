# Dot product and cosine similarity

The dot product is the fundamental operation of ML — every linear layer, every attention score, every embedding comparison is one:

```
a · b = Σ aᵢbᵢ = ‖a‖‖b‖cos(θ)
```

Two readings: (1) a weighted sum (the "sum of products" a neuron computes); (2) a measure of **alignment** — the cosine of the angle between vectors.

## Cosine similarity

```
cos(a,b) = (a·b) / (‖a‖‖b‖)
```

- Normalizes away magnitude: it's the *direction* similarity, in [-1, 1].
- **L2-normalize the vectors first**, then cosine = plain dot product (see [[norms]]).
- The standard similarity for embeddings — words, documents, retrieved chunks (see [[../automl/metric-learning]], [[../nlp]] RAG notes).

## Dot product vs cosine — when each

- **Cosine**: retrieval/similarity where magnitude is irrelevant (embeddings, text).
- **Raw dot product**: attention! `Q·Kᵀ/√d` uses the raw product — magnitude *is* meaningful for attention scores, and the √d scaling keeps the product in a stable range (see [[../deeplearning/attention-is-all-you-need]]).
- **Weighted sum**: every `W·x` in a [[../algorithms/neural-network]] is a stack of dot products.

## The linear-algebra view

- Orthogonal vectors: `a·b = 0` — this is what PCA's components are (see [[../algorithms/PCA]]).
- `a·b` is the projection of `a` onto `b` (scaled) — the geometric meaning behind `⟨xᵢ, xⱼ⟩` in SVMs' kernel trick (see [[../algorithms/SVM]]).
- Matrix multiplication is just batched dot products — the "tensor core" op every GPU spends its time on (see [[Store/deep-learning-hardware]]).

## Related

- [[norms]] — the denominators in cosine.
- [[../automl/metric-learning]] — learning embeddings to make dot products meaningful.
- [[../deeplearning/attention-is-all-you-need]] — dot-product attention.
