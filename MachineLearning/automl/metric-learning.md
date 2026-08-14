# Metric learning

Metric learning is about learning a **distance function** such that semantically similar examples are close and dissimilar ones are far apart, in some learned embedding space. It's the "distance-based" family in [meta-learning](meta-learning.md) / [few-shot-learning](few-shot-learning.md), but it's also useful on its own (search, retrieval, verification).

## The idea

Instead of using a fixed distance (Euclidean in raw space), learn a mapping `f: x → z` and measure similarity as `d(f(x_i), f(x_j))`. If `f` is trained right, nearest-neighbors in `z`-space are the semantically similar items.

## Training objectives

- **Contrastive loss**: pairs (same/different label) pushed together / pulled apart.
- **Triplet loss**: anchor `a`, positive `p`, negative `n` — enforce `d(a,p) + margin < d(a,n)`.
- **N-pair / Multi-Similarity / ArcFace** losses: hard-negative mining, better scaling to big batches.
- **Proxy-based losses**: learn class "proxies" instead of pairwise comparisons — cheaper.

## Where it's used

- Face verification / recognition (FaceNet, ArcFace) — embeddings that are close = same person.
- Image retrieval, product search, duplicate detection.
- Siamese networks → the backbone of metric-based [few-shot-learning](few-shot-learning.md).
- Learned features can feed other classifiers directly (the embedding becomes [automated-feature-extraction](automated-feature-extraction.md)).

## Notes

- Hard negative mining is often more important than the loss choice.
- The learned space is a *representation* — it needs to be (close to) [injective](../algorithms/injectivity.md) to preserve identity info.
