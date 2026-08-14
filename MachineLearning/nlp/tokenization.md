# Tokenization

Tokenization turns raw text into a sequence of integer IDs — the input that every NLP model actually sees. The choice of tokenizer determines the vocabulary, and it's often the thing that silently breaks cross-model/cross-library compatibility.

## The spectrum of approaches

- **Character**: every Unicode char is a token. Tiny vocab, huge sequences, no real meaning units. Almost never used alone.
- **Word**: every whitespace-separated word is a token. Vocab explodes (millions), unknown words are untokenizable, morphology is ignored. Good for CJK languages, bad for everything else.
- **Subword** (the standard): split words into *frequent pieces* — a small vocab (30k–250k) that covers any text as a concatenation of known pieces. Best of both worlds.

## The three subword algorithms

- **BPE (Byte-Pair Encoding)** — the original (GPT, GPT-2):
  1. Start with characters as tokens.
  2. Repeatedly merge the *most frequent adjacent pair* into a new token.
  3. Stop at target vocab size.
  Result: frequent words become single tokens; rare words stay as pieces. Greedy, frequency-based.
- **WordPiece** (BERT): like BPE but merges by *likelihood ratio* (the pair whose merge most increases the data likelihood), not raw count. Subtle, but same spirit.
- **SentencePiece**: BPE/unigram but treats the whole text as a token stream — crucially, it **works on raw bytes or UTF-8, no whitespace preprocessing**. This is how models handle any language (and emoji) — "byte-level" tokenization (GPT-3/4 use byte-level BPE). The **Unigram** variant picks the tokenization (over multiple candidate segmentations) that maximizes likelihood.

## Practical facts I keep rediscovering

- The vocab file *is* part of the model — mismatched tokenizer = garbage in, garbage out. This is the number-one cause of "it worked in my notebook, failed in prod" (a real RAG gotcha, see [[RAG]]).
- Token length ≈ 4 chars ≈ 0.75 words for English BPE; context windows are counted in **tokens**, not words (this is why GPT-4 "8k context" is ~6k words).
- BOS/EOS/PAD/UNK special tokens are real tokenizer state. Padding is what the compiler sees as wasteful (see [[../../Compilers/DLCompilers/TorchInductor/TorchInductor]] and dynamic shape work in [[../../PyTorchCompiler/PLAN]]).

## Related

- [[../deeplearning/embeddings]] — token IDs are the index into the embedding table.
- [[BERT]] / [[GPT-decoder-only-models]] — WordPiece vs BPE in practice.
- [[RAG]] — tokenizer mismatch is a classic retrieval pipeline bug.
