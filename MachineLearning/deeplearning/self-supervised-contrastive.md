# Self-supervised and contrastive learning

Self-supervised learning (SSL) learns representations **from the data itself, without labels** — then those representations are used downstream (linear probe, few-shot fine-tune). Contrastive learning is the dominant SSL family, but not the only one.

## The idea

Instead of a classification loss on `(x, label)`, define a **pretext task** whose supervision comes free from the data:

- **Rotation**: rotate an image by 0/90/180/270°, predict the angle.
- **Masking**: mask a patch (text: BERT's `[MASK]`, images: MAE), predict what was hidden — see [BERT](../nlp/BERT.md).
- **Instance discrimination**: every image is its own class; the model learns "this image ≠ that image".

The point isn't the pretext task itself — it's the **general image/object/word features** the network is forced to learn to solve it. Those features transfer, so a small labeled set downstream goes a long way.

## Contrastive learning

Train an encoder `f` so that **augmented views of the same sample (positive pairs) are pulled together**, and **different samples (negatives) are pushed apart**:

```
positive pair: (augment(x), augment(x))  → embeddings close
negative pair: (x, y), x≠y               → embeddings far
```

- **SimCLR**: per-batch negatives; strong random augmentations (crop, color jitter) define the positives. Simple, effective, but benefits from big batches.
- **MoCo**: keeps a large queue of negatives + a momentum-updated copy of the encoder so negatives don't change too fast — decouples batch size from negative count.
- **BYOL / SimSiam**: **non-contrastive** — *no negatives at all*. Just pull the two views of the same image together, but use a **stop-gradient** (and in BYOL a predictor) on one side, which prevents collapse (everything mapping to the same vector). Collapse is the failure mode contrastive methods exist to avoid.

## The loss: InfoNCE

InfoNCE is a softmax over similarities: for positive pair `(i, j)` and negatives `k`,

```
L = -log( exp(sim(z_i, z_j)/τ) / ( exp(sim(z_i, z_j)/τ) + Σ_k exp(sim(z_i, z_k)/τ) ) )
```

`τ` (temperature) sharpens the distribution. It's "pull the positive, push all negatives, weighted by how hard each negative is" — a scaled-up, batchy version of the pairwise [metric-learning](../automl/metric-learning.md) losses.

## Why it matters

- Pretrain a backbone **on unlabeled data** (usually way more available), then **few-shot fine-tune** or linear-probe on your task — this is how CLIP/ImageNet-scale SSL backbones work, and the same trick powers modern NLP pretraining (see [transfer-learning](../algorithms/transfer-learning.md)).
- The learned space is an [embedding](embeddings.md) space: similarity search, retrieval, and verification all fall out of it for free.

## Related

- [metric-learning](../automl/metric-learning.md) — the pairwise/triplet loss ancestors.
- [embeddings](embeddings.md) — the representation these losses shape.
- [BERT](../nlp/BERT.md) — masked-language modeling is SSL on text.
- [transfer-learning](../algorithms/transfer-learning.md) — the downstream use.
