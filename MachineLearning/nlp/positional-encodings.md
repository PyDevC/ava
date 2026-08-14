# Positional encodings

Transformers have **no inherent notion of order** — attention is a set operation, and "I love you" and "you love I" produce identical attention scores without position information. Positional encodings inject "where am I" into the model (see [attention-is-all-you-need](../deeplearning/attention-is-all-you-need.md)).

## The two classic families

**Absolute (sinusoidal)** — the original from "Attention Is All You Need":
```
PE(pos, 2i)   = sin(pos / 10000^(2i/d))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d))
```
Frequencies decrease along the embedding dimension: early dims encode fine-grained position, later dims coarse. Sinusoids chosen so the model can *learn* to attend to relative offsets. No learnable parameters, generalizes past training length (in theory).

**Learnable absolute** — BERT/GPT: a learned matrix `P ∈ R^(max_len × d)`, position row added to token embeddings. Simple and effective; capped by `max_len` (this is why models have a hard max context).

## The modern fix: relative / rotary

The problem with absolute encodings: they don't *shift* well — position "10" has a fixed vector regardless of what it's relative to. Two modern replacements:

- **Relative position biases** (T5, ALiBi): add a position-dependent *bias* to attention scores (`score += f(i-j)`). With ALiBi it's just `score += -m·|i-j|` — zero learned params and it extrapolates to longer contexts.
- **RoPE (Rotary Position Embedding)** — the default for LLaMA, GPT-NeoX, most modern LLMs: rotate the query/key vectors by an angle proportional to position. It makes the *dot product* (see [dot-product-cosine](../mathematical-function/dot-product-cosine.md)) depend only on the **relative distance** `i-j` — attention naturally decays with distance, and it extrapolates well. This is the "rope" you see in every modern config file.

## Why it matters to me

- Context length is bounded by how positions are represented — the whole "long context" arms race (RoPE base scaling, YaRN, etc.) is about making encodings work past training length.
- KV-cache and sliding-window attention (see [distributed-training](../deeplearning/distributed-training.md) / [GPT-decoder-only-models](GPT-decoder-only-models.md)) interact with position handling.

## Related

- [attention-is-all-you-need](../deeplearning/attention-is-all-you-need.md) — the paper that introduced sinusoidal PE.
- [GPT-decoder-only-models](GPT-decoder-only-models.md) — RoPE/ALiBi in production models.
- [BERT](BERT.md) — learnable absolute PE.
