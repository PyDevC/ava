# Regularization and normalization in deep learning

Deep nets overfit *hard* — enough parameters to memorize the training set. This note collects the NN-specific tools that fight that, plus the normalization layers that make deep training stable.

## Weight-level regularization (the same idea as classic ML)

- **Weight decay** — L2 penalty on weights, `J + ½λ‖w‖²` (see [regularization](../algorithms/regularization.md)). In Adam, *decoupled* weight decay (AdamW) is correct; "weight decay" folded into Adam's updates behaves differently.
- **Dropout** — at training, zero out each neuron with probability `p` (Bernoulli mask, see [bernoulli-categorical](../mathematical-function/bernoulli-categorical.md)). At inference, multiply by `(1-p)` (or use inverted dropout). Effect: the network can't rely on any single unit — trains a noisy *ensemble* of sub-networks, which reduces variance (see [bias-variance-tradeoff](../algorithms/bias-variance-tradeoff.md)).

## Data-level regularization

- **Data augmentation** — random transforms (crops, flips, color jitter, for NLP: token masking) so the model never sees the same example twice. The most effective regularizer in practice; it expands the dataset distribution itself.
- **Early stopping** — stop when validation loss stops improving; the weights have only traveled so far (see [cross-validation](../algorithms/cross-validation.md)).
- **Label smoothing** — soften one-hot targets (mix with uniform): keeps softmax from over-confident predictions.

## Batch normalization

```
y = γ·(x - μ_batch)/√(σ²_batch + ε) + β
```

Normalizes each feature across the **batch**, then re-scales/re-shifts with *learned* `γ, β`.

- Fixes **internal covariate shift**: keeps activations in a stable range so deep nets train with high LRs.
- `μ, σ` are batch statistics at train time; at *inference*, running averages are used instead — so **train/inference differ** (a classic source of "works in training, breaks at deployment" bugs).
- In regularization terms, batch noise (batch statistics vary) acts as a mild regularizer.

## Layer normalization

Normalizes each sample over its **feature dimension** instead of the batch — no dependence on batch size, same behavior at train/inference. This is why **Transformers use LayerNorm**, not BatchNorm (see [Transformers](Transformers.md) and the [attention-is-all-you-need](attention-is-all-you-need.md) notes). Zero-shot/few-shot and small-batch training also favor it.

## Which to reach for

- Vision CNNs: BatchNorm (fused into conv by inference engines; see the conv note in [An-introduction-to-convolutional-neural-networks](An-introduction-to-convolutional-neural-networks.md)).
- Transformers / RNNs / small batches: LayerNorm.
- Dropout: Transformers prefer *no* dropout in attention post-2020 (or very little); CNNs use it before the classifier.

## Related

- [regularization](../algorithms/regularization.md) — the classic ML framing these generalize.
- [model-optimization](model-optimization.md) — how weight decay plugs into the optimizer.
- [how-to-use-lr-scheduler](how-to-use-lr-scheduler.md) — normalization is what lets you train with aggressive LR schedules.
