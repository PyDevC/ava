# MachineLearning/nlp — Plan

## Goal of this directory

Cover **Natural Language Processing** — the field where my ML interest most strongly connects to the compiler work (the Transformers story runs through both). Currently the thinnest directory in the repo; the goal is to build a solid NLP foundation: representations → architectures → applications → evaluation.

## What's already here

- [[BERT]] — the bidirectional encoder transformer, fine-tuning story.
- Representations: [[tokenization]], [[positional-encodings]], [[embeddings-for-search]]
- Architectures: [[GPT-decoder-only-models]], [[seq2seq-attention]], [[T5-encoder-decoder]]
- Tasks: [[nlp-tasks-fine-tuning]], [[instruction-tuning-rlhf]]
- Applications: [[RAG]], [[prompting-in-context-learning]], [[agents-tool-calling]]
- Evaluation: [[GPT-evaluation]]

## What to add next

- [x] **Representations first**:
  - [x] Tokenization (BPE, WordPiece, SentencePiece) — the input preprocessing
  - [x] Embeddings: word2vec/GloVe → contextual (BERT-style) — why context changed everything (see [[../deeplearning/embeddings]] + [[embeddings-for-search]])
  - [x] Positional encodings (referred to in [[../deeplearning/attention-is-all-you-need]])
- [x] **Architectures** (the encoder/decoder map):
  - [x] GPT / decoder-only models — the generation side of the story [[BERT]] opens
  - [x] seq2seq + attention (bridge RNN → Transformer; see [[../deeplearning]] plan)
  - [x] T5 (encoder-decoder) — completes the three-way split
- [x] **Tasks & fine-tuning**:
  - [x] Classification (sentiment), NER, QA, translation — the task heads
  - [x] Instruction fine-tuning / RLHF (how we get "chat" models)
- [x] **Modern LLM applications** (very relevant to my interests):
  - [x] RAG (retrieval-augmented generation) — the production pattern
  - [x] Prompting / in-context learning (ties to [[../automl/meta-learning]])
  - [x] Agents and tool calling
  - [x] Embeddings for search/retrieval (ties to [[../automl/metric-learning]])
- [x] **Evaluation**:
  - [x] Perplexity, BLEU, ROUGE, and why they're crude
  - [x] Modern LLM evals (benchmarks, human eval, task-specific metrics)

## Prioritization

1. **Tokenization + embeddings + GPT/decoder** — the minimal complete story from [[BERT]].
2. **RAG + evals** — the parts I'll actually build and measure.
3. **seq2seq + fine-tuning mechanics** — to understand where these ideas came from.
