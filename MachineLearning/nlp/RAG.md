# RAG (Retrieval-Augmented Generation)

RAG grounds an LLM's answers in a **retrieval step** — instead of relying only on parametric memory (which hallucinates and is stale), the model first fetches relevant documents and generates *conditioned on them*. The standard production pattern for LLM apps.

## The pipeline

```
query → embed → [vector store: top-k] → retrieve docs → prompt(model, query, docs) → answer
```

1. **Index**: split documents into chunks → embed each chunk (see [embeddings-for-search](embeddings-for-search.md)) → store in a vector DB (FAISS, pgvector, Chroma, etc.).
2. **Retrieve**: embed the query, find the k nearest chunks (ANN, see [metric-learning](../automl/metric-learning.md)).
3. **Generate**: stuff the chunks into the context window with the query, prompt the LLM to answer *from the context*.

## The failure modes (this is where the real work is)

- **Bad retrieval** → wrong/irrelevant context → confident hallucination. Retrieval quality is the bottleneck, not the LLM:
  - **Chunking**: too big = diluted relevance, too small = lost context. Overlap and structure-aware splits (by heading) help.
  - **Embedding mismatch**: query embedding and document embedding trained differently; short queries vs long docs. Query rewriting/expansion helps.
  - **Tokenizer mismatch** between embedding model and generator — a classic silent bug (see [tokenization](tokenization.md)).
  - **Filtering**: hybrid search (keyword BM25 + vector) + reranking (cross-encoder) beats raw vector search on most production data.
- **Context budget**: retrieved docs compete with instructions inside the window — a real compiler-style optimization (see [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md)).
- **Groundedness**: the model can still ignore the context. Prompt discipline + post-hoc citation checks ("did the answer's claims appear in the docs?") are the mitigations.

## Why it matters to me

- It's the practical, shippable LLM application — and it connects the ML world (embeddings, [metric-learning](../automl/metric-learning.md)) to the systems world (vector DBs, serving, latency budgets, the same optimization thinking as [DLCompilers](../../Compilers/DLCompilers/)).
- It's also the natural place for the *evaluation* machinery (see [GPT-evaluation](GPT-evaluation.md)): groundedness, faithfulness, and retrieval recall are all measurable.

## Related

- [embeddings-for-search](embeddings-for-search.md) — the retrieval representation.
- [GPT-evaluation](GPT-evaluation.md) — how to measure a RAG pipeline.
- [metric-learning](../automl/metric-learning.md) — contrastive/margin losses behind good retrievers.
- [embeddings](../deeplearning/embeddings.md) — contextual embeddings are the modern retriever.
