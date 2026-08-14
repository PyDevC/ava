# NLP tasks and fine-tuning

The practical workhorse: take a pretrained transformer and **fine-tune** it for a specific task — usually by adding a small task head and training on labeled data. This is how [BERT](BERT.md) and friends became practical tools.

## The two fine-tuning families

- **Task-head fine-tuning** (the classic): the pretrained body is frozen or lightly tuned; a small *new* head is added on top (a linear layer for classification, a span predictor for QA). Train the whole thing briefly on task data.
- **Full / adapter fine-tuning**: tune all weights (expensive, per-task model copies) or insert small **adapters / LoRA** (low-rank updates) so each task is a small delta instead of a full copy — the standard for LLM fine-tuning (see [transfer-learning](../algorithms/transfer-learning.md)).

## The standard task map

- **Classification** (sentiment, spam, topic): pool the sequence (e.g. `[CLS]` for BERT) → linear head → softmax (see [softmax-function](../mathematical-function/softmax-function.md), [logistic-regression](../algorithms/logistic-regression.md)).
- **NER (named entity recognition)**: predict a label *per token* (BIO tagging) → each position gets a classification. Sequence-labeling head.
- **QA (extractive)**: predict start/end spans from the context → two linear heads over the passage. Generative QA (the modern way) is just text-to-text (see [T5-encoder-decoder](T5-encoder-decoder.md)).
- **Translation / summarization**: encoder-decoder with a generation head (see [seq2seq-attention](seq2seq-attention.md)); evaluated with BLEU/ROUGE (see [GPT-evaluation](GPT-evaluation.md)).

## The fine-tuning loop details

- **Train/val split discipline** — see [cross-validation](../algorithms/cross-validation.md); a leaked eval set is the #1 silent failure.
- **Class imbalance** — see [imbalanced-classification](../algorithms/imbalanced-classification.md) (NER and sentiment are usually skewed).
- **Learning rate**: tiny (BERT was ~2e-5) — you're doing a *small perturbation* of a good solution, not random init (see [how-to-use-lr-scheduler](../deeplearning/how-to-use-lr-scheduler.md)).
- **Catastrophic forgetting**: tuning the body on task data erodes general knowledge — the argument for freezing/adapters/LoRA.
- **Token budgets**: max_length truncation and padding (see [tokenization](tokenization.md)) — and padding waste is what the shape-specialization story in [TorchInductor](../../Compilers/DLCompilers/TorchInductor/TorchInductor.md) eliminates.

## Related

- [BERT](BERT.md) — the canonical encoder fine-tuning story.
- [T5-encoder-decoder](T5-encoder-decoder.md) / [GPT-decoder-only-models](GPT-decoder-only-models.md) — the generator side.
- [transfer-learning](../algorithms/transfer-learning.md) — the conceptual frame.
- [regularization-normalization](../deeplearning/regularization-normalization.md) — the training-stability toolbox.
