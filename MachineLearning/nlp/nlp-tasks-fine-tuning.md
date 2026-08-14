# NLP tasks and fine-tuning

The practical workhorse: take a pretrained transformer and **fine-tune** it for a specific task — usually by adding a small task head and training on labeled data. This is how [[BERT]] and friends became practical tools.

## The two fine-tuning families

- **Task-head fine-tuning** (the classic): the pretrained body is frozen or lightly tuned; a small *new* head is added on top (a linear layer for classification, a span predictor for QA). Train the whole thing briefly on task data.
- **Full / adapter fine-tuning**: tune all weights (expensive, per-task model copies) or insert small **adapters / LoRA** (low-rank updates) so each task is a small delta instead of a full copy — the standard for LLM fine-tuning (see [[../algorithms/transfer-learning]]).

## The standard task map

- **Classification** (sentiment, spam, topic): pool the sequence (e.g. `[CLS]` for BERT) → linear head → softmax (see [[../mathematical-function/softmax-function]], [[../algorithms/logistic-regression]]).
- **NER (named entity recognition)**: predict a label *per token* (BIO tagging) → each position gets a classification. Sequence-labeling head.
- **QA (extractive)**: predict start/end spans from the context → two linear heads over the passage. Generative QA (the modern way) is just text-to-text (see [[T5-encoder-decoder]]).
- **Translation / summarization**: encoder-decoder with a generation head (see [[seq2seq-attention]]); evaluated with BLEU/ROUGE (see [[GPT-evaluation]]).

## The fine-tuning loop details

- **Train/val split discipline** — see [[../algorithms/cross-validation]]; a leaked eval set is the #1 silent failure.
- **Class imbalance** — see [[../algorithms/imbalanced-classification]] (NER and sentiment are usually skewed).
- **Learning rate**: tiny (BERT was ~2e-5) — you're doing a *small perturbation* of a good solution, not random init (see [[how-to-use-lr-scheduler]]).
- **Catastrophic forgetting**: tuning the body on task data erodes general knowledge — the argument for freezing/adapters/LoRA.
- **Token budgets**: max_length truncation and padding (see [[tokenization]]) — and padding waste is what the shape-specialization story in [[Compilers/DLCompilers/TorchInductor/TorchInductor]] eliminates.

## Related

- [[BERT]] — the canonical encoder fine-tuning story.
- [[T5-encoder-decoder]] / [[GPT-decoder-only-models]] — the generator side.
- [[../algorithms/transfer-learning]] — the conceptual frame.
- [[../deeplearning/regularization-normalization]] — the training-stability toolbox.
