---
id: BERT
aliases: []
tags: []
---

# BERT

BERT stands for BiDirectional Encoder Representation with transformers
BERT release was followed after the release of three architectures that also achieved state-of-the-art performances. These models were: 

- ULM-Fit 
- ELMo 
- OpenAI GPT 
- BERT 

## What makes BERT different

Previous NLP models read text **left-to-right only** (or right-to-left only). BERT uses a **bidirectional Transformer encoder** — every token attends to *both* its left and right context at the same time. That bidirectionality is why it captures context so much better than GPT (which is unidirectional/left-to-right).

## The pretraining task (masked language modeling)

BERT is trained on unlabeled text with two objectives:

1. **Masked LM (MLM)**: randomly mask ~15% of tokens; predict the masked words from surrounding context. This forces bidirectional understanding.
2. **Next Sentence Prediction (NSP)**: given a pair of sentences, predict whether the second follows the first. This teaches sentence-pair/relationship understanding (needed for QA, NLI).

## Two sizes

- **BERT-Base**: 12 layers, 12 heads, 110M params.
- **BERT-Large**: 24 layers, 16 heads, 340M params.

## How it's used

**Fine-tuning**: take the pretrained model, swap the output head for a task head, and train briefly on a small labeled dataset. That's the [[MachineLearning/algorithms/transfer-learning]] story — the pretrained encoder gives you language understanding "for free", and you only learn a small task-specific layer.

- Classification (sentiment, spam) → [CLS] token + classifier head.
- QA → predict start/end spans.
- NER → per-token labels.

## Related

- Built on the Transformer encoder — see [[MachineLearning/deeplearning/Transformers]] and the [[MachineLearning/deeplearning/attention-is-all-you-need]] notes.
- Follow-ups: RoBERTa (better training, no NSP), DistilBERT (smaller), ALBERT (shared params).
