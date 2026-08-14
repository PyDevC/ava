# MachineLearning/nlp — Plan

## Goal of this directory

Cover **Natural Language Processing** — the field where my ML interest most strongly connects to the compiler work (the Transformers story runs through both). Currently the thinnest directory in the repo; the goal is to build a solid NLP foundation: representations → architectures → applications → evaluation.

## What's already here

- [[BERT]] — the bidirectional encoder transformer, fine-tuning story.

## What to add next

- [ ] **Representations first**:
  - [ ] Tokenization (BPE, WordPiece, SentencePiece) — the input preprocessing
  - [ ] Embeddings: word2vec/GloVe → contextual (BERT-style) — why context changed everything
  - [ ] Positional encodings (referred to in [[../deeplearning/attention-is-all-you-need]])
- [ ] **Architectures** (the encoder/decoder map):
  - [ ] GPT / decoder-only models — the generation side of the story [[BERT]] opens
  - [ ] seq2seq + attention (bridge RNN → Transformer; see [[../deeplearning]] plan)
  - [ ] T5 (encoder-decoder) — completes the three-way split
- [ ] **Tasks & fine-tuning**:
  - [ ] Classification (sentiment), NER, QA, translation — the task heads
  - [ ] Instruction fine-tuning / RLHF (how we get "chat" models)
- [ ] **Modern LLM applications** (very relevant to my interests):
  - [ ] RAG (retrieval-augmented generation) — the production pattern
  - [ ] Prompting / in-context learning (ties to [[../automl/meta-learning]])
  - [ ] Agents and tool calling
  - [ ] Embeddings for search/retrieval (ties to [[../automl/metric-learning]])
- [ ] **Evaluation**:
  - [ ] Perplexity, BLEU, ROUGE, and why they're crude
  - [ ] Modern LLM evals (benchmarks, human eval, task-specific metrics)

## Prioritization

1. **Tokenization + embeddings + GPT/decoder** — the minimal complete story from [[BERT]].
2. **RAG + evals** — the parts I'll actually build and measure.
3. **seq2seq + fine-tuning mechanics** — to understand where these ideas came from.
