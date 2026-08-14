# Embeddings

An embedding maps discrete items (words, users, images patches) to **dense vectors** where semantic similarity = spatial proximity. It's the representation layer that makes neural nets work on categorical data.

## The core idea

- One-hot vectors are sparse, high-dim, and say nothing about *meaning* ("dog" and "cat" are orthogonal).
- Embeddings are *learned* vectors in a low-dim space (e.g. 128–768 dims): similar items land near each other. "Near" is measured by dot product / cosine (see [dot-product-cosine](../mathematical-function/dot-product-cosine.md)).
- Every embedding is a learned lookup table (`Embedding(num_items, dim)` = a matrix `W` where row `i` is item `i`'s vector).

## Word embeddings (the classic)

- **word2vec / GloVe**: train so that words in similar contexts have similar vectors — "king − man + woman ≈ queen". Context = co-occurrence statistics.
- The **key limitation**: static — "bank" has one vector whether it means river or money. Fixed embeddings can't do polysemy.

## Contextual embeddings (the modern story)

- **ELMo → BERT → GPT**: the embedding isn't a lookup — it's the *output of a Transformer layer conditioned on context* (see [BERT](../nlp/BERT.md)). "Bank" near "river" vs near "money" gets different vectors.
- This is why modern NLP is "pretrain an encoder, use its hidden states as embeddings" (see [transfer-learning](../algorithms/transfer-learning.md)).

## Where embeddings show up

- **NLP**: tokens → embeddings → transformer (the input layer of every LLM).
- **Recommendation/systems**: user and item embeddings, similarity = recommendation.
- **Representation learning**: any learned latent space — autoencoders, contrastive [metric-learning](../automl/metric-learning.md).
- **Geometric meaning**: good embeddings are (nearly) [injective](../algorithms/injectivity.md) — distinct items stay distinct — and structured (clusters, analogies).

## Why they're efficient

The Embedding layer is *the* fastest op in any model: a matrix gather, no compute. The cost is all memory (the table). This matters for serving giant vocabularies.

## Related

- [Transformers](Transformers.md) / [BERT](../nlp/BERT.md) — contextual embeddings.
- [metric-learning](../automl/metric-learning.md) — the distance-learning view.
- [dot-product-cosine](../mathematical-function/dot-product-cosine.md) — how similarity is measured.
