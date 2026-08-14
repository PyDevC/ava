# Transformers

The Transformer is the architecture from "Attention is all you need" (2017) — see [[attention-is-all-you-need]] for my paper notes. It replaced RNNs/CNNs for sequences because it is **fully parallelizable** (no sequential recurrence) and scales massively with data and compute. It's now the backbone of modern NLP (BERT, GPT), vision (ViT), and much of multimodal AI.

## The core building blocks

1. **Input embeddings + positional encodings** — words/patches become vectors; positional info is injected because the model has no built-in notion of order (unlike recurrence). See the softmax mention in the paper notes — the final output layer uses softmax over the vocabulary.

2. **Multi-head self-attention** — each token looks at every other token (weighted by learned compatibility) and mixes their representations. This gives the model a global view in one layer — the big advantage over convolutions/recurrence.

3. **Feed-forward block** (position-wise MLP) — applies the same two linear layers + activation to each position independently. Adds per-token non-linear capacity.

4. **Residual connections + LayerNorm** around each sub-block — the `x + Sublayer(x)` and normalization that keeps gradients from vanishing (see the paper notes).

Stack these N times → you have an encoder or a decoder. Encoder-only (BERT) for understanding, decoder-only (GPT) for generation, encoder-decoder (T5) for translation.

## Why "the future is transformers"

- **Parallel training**: no recurrence → trains on GPUs in parallel over all tokens. This is why scale exploded.
- **Scaling laws**: bigger models + more data keep helping smoothly (unlike earlier architectures).
- **In-context learning**: decoder-only models can solve novel tasks from a few prompt examples — a surprising emergent behavior (see [[MachineLearning/automl/meta-learning]] for the connection).
- **One architecture for everything**: text, images, audio, graphs — as tokens/patches.

## Related

- [[attention-is-all-you-need]] — the paper review.
- [[An-introduction-to-convolutional-neural-networks]] — the older vision workhorse.
- [[MachineLearning/nlp/BERT]] — the encoder-only application.
