# Seq2seq + attention

Sequence-to-sequence models **map one sequence to another** — the architecture behind neural machine translation, summarization, and originally chat. It's the historical bridge between RNNs and Transformers.

## The encoder-decoder frame

- **Encoder**: reads the whole source sequence into a context (initially: the final hidden state; then: a set of states, then attention).
- **Decoder**: generates the target sequence token-by-token, conditioned on the source representation.

## The RNN era and its fatal flaw

Early seq2seq (Sutskever et al. 2014) compressed the entire source into **one fixed vector** (the encoder's last hidden state). Long inputs → the context vector can't hold it all → translation quality collapses on long sentences (the [[../deeplearning/RNN-LSTM-GRU]] long-term memory problem, again).

## Attention fixes it (Bahdanau 2015 → Transformer 2017)

**Attention** = don't compress the whole source; let the decoder **look back** at all encoder states, weighted by relevance at each decoding step:

```
context_t = Σᵢ α_tᵢ · hᵢ     where  α_tᵢ = softmax(score(q_t, kᵢ))
```

This is the direct ancestor of the Transformer's self-attention ([[../deeplearning/attention-is-all-you-need]]). Two generations:

- **Additive attention** (Bahdanau): `score = W·tanh(Wₐq + W_b k)` — a small MLP.
- **Dot-product attention** (Vaswani): `score = q·k / √d` — the modern standard (see [[../mathematical-function/dot-product-cosine]]).

## The sequence of leaps

1. RNN seq2seq — fixed context, forgets long inputs.
2. + attention — decoder attends to encoder states (still RNN-based; this is the "Bahdanau" model).
3. Transformer — **self-attention everywhere**, no RNN at all: parallelizable, trainable on GPUs, long-range. This is what killed the RNN stack.
4. T5 / BART — modern encoder-decoder Transformers (see [[T5-encoder-decoder]]).

## Related

- [[../deeplearning/attention-is-all-you-need]] — the Transformer paper, direct descendant.
- [[../deeplearning/RNN-LSTM-GRU]] — the encoder/decoder base it replaced.
- [[T5-encoder-decoder]] — the modern encoder-decoder.
- [[../deeplearning/embeddings]] — the representation layer under it all.
