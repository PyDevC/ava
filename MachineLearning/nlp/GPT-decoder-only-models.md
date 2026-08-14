# GPT / decoder-only models

The decoder-only Transformer is the architecture behind ChatGPT, LLaMA, Mistral, and basically every modern LLM. It's the "generation" side of the encoder/decoder map ([BERT](BERT.md) is the encoder side).

## The setup

- **Causal masking**: each token can only attend to itself and earlier tokens (a lower-triangular mask on attention scores). Nothing leaks from the future.
- **Objective: next-token prediction** — given the prefix, predict the next token (cross-entropy, see [loss-function](../algorithms/loss-function.md)). That's it. The whole capability comes from scale, data, and post-training.
- **Generation**: at inference you sample one token, append it, and re-run — the **autoregressive loop**. This is where the KV cache lives (avoid recomputing past keys/values), and it's why LLM inference is memory-bandwidth-bound (see [Roadmaps](../../Roadmaps/PLAN.md) and [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md)).

## Why decoder-only won

- **Unified**: the same model does completion, and with instruction tuning (see [instruction-tuning-rlhf](instruction-tuning-rlhf.md)) answers questions, writes code, etc. No separate encoder/decoder to align.
- **Scaling law-friendly**: the objective is embarrassingly simple to scale (predict the next token over web-scale data).
- Attention masking lets you still do bidirectional-style understanding with prefix prompting.

## The training pipeline (three stages)

1. **Pretraining**: next-token prediction on trillions of tokens (see [transfer-learning](../algorithms/transfer-learning.md)). ~99.9% of compute.
2. **SFT (supervised fine-tuning)**: train on human-written instruction→response pairs (see [instruction-tuning-rlhf](instruction-tuning-rlhf.md)).
3. **RLHF / alignment**: preferences (see [instruction-tuning-rlhf](instruction-tuning-rlhf.md) again).

## Key implementation details

- **KV cache**: store K/V from processed tokens; during generation only compute the *new* token's attention. Memory grows linearly with sequence length — the fundamental inference cost.
- **GQA (grouped-query attention)**: share K/V heads across groups of query heads → fewer KV-cache bytes, less memory bandwidth (Mistral, LLaMA 2/3).
- **RoPE / ALiBi** for positions (see [positional-encodings](positional-encodings.md)).
- **Sliding window attention** (Mistral): limit attention to the last `w` tokens → constant memory per step, handles long sequences.

## Related

- [BERT](BERT.md) — the encoder contrast (bidirectional, for understanding).
- [positional-encodings](positional-encodings.md) — RoPE, the position story.
- [tokenization](tokenization.md) — byte-level BPE, the input layer.
- [instruction-tuning-rlhf](instruction-tuning-rlhf.md) — how a predictor becomes a chatbot.
- [distributed-training](../deeplearning/distributed-training.md) — FSDP + tensor parallelism to train these.
