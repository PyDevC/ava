# Embeddings

An embedding maps discrete items (words, users, images patches) to **dense vectors** where semantic similarity = spatial proximity. It's the representation layer that makes neural nets work on categorical data.

## The core idea

- One-hot vectors are sparse, high-dim, and say nothing about *meaning* ("dog" and "cat" are orthogonal).
- Embeddings are *learned* vectors in a low-dim space (e.g. 128–768 dims): similar items land near each other. "Near" is measured by dot product / cosine (see [[../mathematical-function/dot-product-cosine]]).
- Every embedding is a learned lookup table (`Embedding(num_items, dim)` = a matrix `W` where row `i` is item `i`'s vector).

## Word embeddings (the classic)

- **word2vec / GloVe**: train so that words in similar contexts have similar vectors — "king − man + woman ≈ queen". Context = co-occurrence statistics.
- The **key limitation**: static — "bank" has one vector whether it means river or money. Fixed embeddings can't do polysemy.

## Contextual embeddings (the modern story)

- **ELMo → BERT → GPT**: the embedding isn't a lookup — it's the *output of a Transformer layer conditioned on context* (see [[../nlp/BERT]]). "Bank" near "river" vs near "money" gets different vectors.
- This is why modern NLP is "pretrain an encoder, use its hidden states as embeddings" (see [[../algorithms/transfer-learning]]).

## Where embeddings show up

- **NLP**: tokens → embeddings → transformer (the input layer of every LLM).
- **Recommendation/systems**: user and item embeddings, similarity = recommendation.
- **Representation learning**: any learned latent space — autoencoders, contrastive [[../automl/metric-learning]].
- **Geometric meaning**: good embeddings are (nearly) [[../algorithms/injectivity|injective]] — distinct items stay distinct — and structured (clusters, analogies).

## Why they're efficient

The Embedding layer is *the* fastest op in any model: a matrix gather, no compute. The cost is all memory (the table). This matters for serving giant vocabularies.

## Related

- [[Transformers]] / [[../nlp/BERT]] — contextual embeddings.
- [[../automl/metric-learning]] — the distance-learning view.
- [[../mathematical-function/dot-product-cosine]] — how similarity is measured.
