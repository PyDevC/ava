# Embeddings for search/retrieval

Retrieval systems (RAG, semantic search, dedup) run on **embedding similarity**: embed everything, then find nearest neighbors. The quality of the *retriever* determines the quality of everything downstream.

## The setup

- Corpus: embed every document/chunk into a vector space (see [embeddings](../deeplearning/embeddings.md)).
- Query: embed, find top-k by cosine similarity (see [dot-product-cosine](../mathematical-function/dot-product-cosine.md)).
- Scale: exact kNN is O(N); production uses **ANN** (approximate nearest neighbor) — HNSW (graph-based), IVF (inverted-file), PQ (product quantization), FAISS/Annoy/ScANNS. "Recall@k" is the metric.

## How retrieval embeddings are trained (the key difference vs generic embeddings)

- Generic word embeddings (word2vec) are *static* and bad at retrieval semantics.
- Retrieval embeddings are trained with **contrastive / metric learning** (see [metric-learning](../automl/metric-learning.md)): pull `(query, positive_doc)` pairs together, push negatives apart. The two dominant designs:
  - **Bi-encoder** (e.g. OpenAI/BGE/MiniLM embeddings): query and doc encoded independently → fast at scale (precompute doc vectors). Standard for ANN search.
  - **Cross-encoder** (e.g. rerankers): query+doc passed *together* through a model → slow but far more accurate. Used as the **reranking** stage on top of a bi-encoder's top-k.
- Training data: mined pairs from click data, §-level passage retrieval datasets (MS MARCO), contrastive with hard negatives (see [imbalanced-classification](../algorithms/imbalanced-classification.md) for why negatives matter).

## The production pattern

1. Bi-encoder: cheap recall (top ~100).
2. Cross-encoder reranker: precision on that 100 → top 5.
3. Hybrid: BM25 (keyword) fused with vector scores beats either alone on in-domain data — because embeddings miss exact-match facts.

## Practical gotchas

- **Embedding drift**: re-embedding the corpus after a model update changes the space — version your index.
- **Dimensionality**: 768-dim vs 384-dim matters for index size and recall; Matryoshka embeddings let you *truncate* and keep utility (nested/truncatable vectors).
- **Normalization**: cosine on unnormalized vectors is not equivalent to dot product — normalize or scale consistently (see [norms](../mathematical-function/norms.md)).

## Related

- [RAG](RAG.md) — the application built on this.
- [metric-learning](../automl/metric-learning.md) — the training objective family.
- [dot-product-cosine](../mathematical-function/dot-product-cosine.md) — the similarity function.
- [PCA](../algorithms/PCA.md) — old-school dimensionality reduction, still used to compress indexes.
