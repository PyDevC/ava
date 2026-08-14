# MachineLearning/nlp — Plan

## Goal of this directory

Cover **Natural Language Processing** — the field where my ML interest most strongly connects to the compiler work (the Transformers story runs through both). Currently the thinnest directory in the repo; the goal is to build a solid NLP foundation: representations → architectures → applications → evaluation.

## What's already here

- [BERT](BERT.md) — the bidirectional encoder transformer, fine-tuning story.
- Representations: [tokenization](tokenization.md), [positional-encodings](positional-encodings.md), [embeddings-for-search](embeddings-for-search.md)
- Architectures: [GPT-decoder-only-models](GPT-decoder-only-models.md), [seq2seq-attention](seq2seq-attention.md), [T5-encoder-decoder](T5-encoder-decoder.md)
- Tasks: [nlp-tasks-fine-tuning](nlp-tasks-fine-tuning.md), [instruction-tuning-rlhf](instruction-tuning-rlhf.md)
- Applications: [RAG](RAG.md), [prompting-in-context-learning](prompting-in-context-learning.md), [agents-tool-calling](agents-tool-calling.md)
- Evaluation: [GPT-evaluation](GPT-evaluation.md)

## What to add next

- [x] **Representations first**:
  - [x] Tokenization (BPE, WordPiece, SentencePiece) — the input preprocessing
  - [x] Embeddings: word2vec/GloVe → contextual (BERT-style) — why context changed everything (see [embeddings](../deeplearning/embeddings.md) + [embeddings-for-search](embeddings-for-search.md))
  - [x] Positional encodings (referred to in [attention-is-all-you-need](../deeplearning/attention-is-all-you-need.md))
- [x] **Architectures** (the encoder/decoder map):
  - [x] GPT / decoder-only models — the generation side of the story [BERT](BERT.md) opens
  - [x] seq2seq + attention (bridge RNN → Transformer; see [deeplearning](../deeplearning/PLAN.md) plan)
  - [x] T5 (encoder-decoder) — completes the three-way split
- [x] **Tasks & fine-tuning**:
  - [x] Classification (sentiment), NER, QA, translation — the task heads
  - [x] Instruction fine-tuning / RLHF (how we get "chat" models)
- [x] **Modern LLM applications** (very relevant to my interests):
  - [x] RAG (retrieval-augmented generation) — the production pattern
  - [x] Prompting / in-context learning (ties to [meta-learning](../automl/meta-learning.md))
  - [x] Agents and tool calling
  - [x] Embeddings for search/retrieval (ties to [metric-learning](../automl/metric-learning.md))
- [x] **Evaluation**:
  - [x] Perplexity, BLEU, ROUGE, and why they're crude
  - [x] Modern LLM evals (benchmarks, human eval, task-specific metrics)

## Prioritization

1. **Tokenization + embeddings + GPT/decoder** — the minimal complete story from [BERT](BERT.md).
2. **RAG + evals** — the parts I'll actually build and measure.
3. **seq2seq + fine-tuning mechanics** — to understand where these ideas came from.
