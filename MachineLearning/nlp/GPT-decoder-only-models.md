# GPT / decoder-only models

The decoder-only Transformer is the architecture behind ChatGPT, LLaMA, Mistral, and basically every modern LLM. It's the "generation" side of the encoder/decoder map ([[BERT]] is the encoder side).

## The setup

- **Causal masking**: each token can only attend to itself and earlier tokens (a lower-triangular mask on attention scores). Nothing leaks from the future.
- **Objective: next-token prediction** — given the prefix, predict the next token (cross-entropy, see [[../algorithms/loss-function]]). That's it. The whole capability comes from scale, data, and post-training.
- **Generation**: at inference you sample one token, append it, and re-run — the **autoregressive loop**. This is where the KV cache lives (avoid recomputing past keys/values), and it's why LLM inference is memory-bandwidth-bound (see [[../../Roadmaps]] and [[../deeplearning/model-optimization-for-inference]]).

## Why decoder-only won

- **Unified**: the same model does completion, and with instruction tuning (see [[instruction-tuning-rlhf]]) answers questions, writes code, etc. No separate encoder/decoder to align.
- **Scaling law-friendly**: the objective is embarrassingly simple to scale (predict the next token over web-scale data).
- Attention masking lets you still do bidirectional-style understanding with prefix prompting.

## The training pipeline (three stages)

1. **Pretraining**: next-token prediction on trillions of tokens (see [[../algorithms/transfer-learning]]). ~99.9% of compute.
2. **SFT (supervised fine-tuning)**: train on human-written instruction→response pairs (see [[instruction-tuning-rlhf]]).
3. **RLHF / alignment**: preferences (see [[instruction-tuning-rlhf]] again).

## Key implementation details

- **KV cache**: store K/V from processed tokens; during generation only compute the *new* token's attention. Memory grows linearly with sequence length — the fundamental inference cost.
- **GQA (grouped-query attention)**: share K/V heads across groups of query heads → fewer KV-cache bytes, less memory bandwidth (Mistral, LLaMA 2/3).
- **RoPE / ALiBi** for positions (see [[positional-encodings]]).
- **Sliding window attention** (Mistral): limit attention to the last `w` tokens → constant memory per step, handles long sequences.

## Related

- [[BERT]] — the encoder contrast (bidirectional, for understanding).
- [[positional-encodings]] — RoPE, the position story.
- [[tokenization]] — byte-level BPE, the input layer.
- [[instruction-tuning-rlhf]] — how a predictor becomes a chatbot.
- [[../deeplearning/distributed-training]] — FSDP + tensor parallelism to train these.
