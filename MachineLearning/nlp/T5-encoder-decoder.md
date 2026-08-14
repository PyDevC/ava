# T5 (encoder-decoder Transformer)

T5 ("Text-to-Text Transfer Transformer", Raffel et al. 2020) reformulates **every NLP task as text-to-text**: all tasks share one model, one loss, one tokenizer.

```
input:  "translate English to German: The house is wonderful."  →  "Das Haus ist wunderbar."
input:  "summarize: <long article>"                             →  "<summary>"
input:  "stsb sentence1: ... sentence2: ..."                    →  "4.0"
```

## Why this matters

- **Unification**: classification becomes generation of the label word; regression becomes generation of the number. One architecture, one objective, zero task-specific heads.
- **Beam search works everywhere**: because outputs are text, decoding-time tricks (beam search, temperature) apply to every task.
- This is the "text-to-text" idea that modern instruction-tuned models (see [[instruction-tuning-rlhf]]) inherit — "format the problem as text, generate the answer".

## Architecture notes

- **Encoder-decoder Transformer** (the "complete" Transformer from [[seq2seq-attention]], unlike decoder-only [[GPT-decoder-only-models]] and encoder-only [[BERT]]).
- **Relative position biases** (see [[positional-encodings]]) — learned per attention head.
- Trained on **C4** (Colossal Clean Crawled Corpus, ~750GB of filtered web text) with a span-corruption objective: mask random spans, predict them. Equivalent to BERT-style masking but as text-to-text.

## Sizes and the modern status

- T5-Small → T5-Large → T5-3B → T5-11B ("XXL"). The size ladder and its scaling story became the template for later LLM families.
- Modern successors: **Flan-T5** (instruction-tuned T5), **UL2** — but for generation at scale, decoder-only won (see [[GPT-decoder-only-models]]). T5-class models still shine for tasks needing strong *bidirectional* context + generation (summarization, grammar).

## Related

- [[BERT]] — the encoder-only ancestor it supersedes for generation.
- [[GPT-decoder-only-models]] — the decoder-only competitor.
- [[instruction-tuning-rlhf]] — the text-to-text idea taken to chat models.
- [[seq2seq-attention]] — the encoder-decoder frame.
