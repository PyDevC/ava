# Image data augmentation

Augmentation is the strongest regularization for vision models (see [[deeplearning/regularization-normalization]]) — every transform produces a new training example, so the model sees the distribution's *variability* instead of memorizing pixel patterns.

## The practical recipe

- **Geometric** (cheap, huge effect): random crop/resize, horizontal flip (NOT vertical — gravity), small rotations (±10–20°), slight scaling, translation. For gesture/landmark systems, the model must generalize across hand poses, so rotation/scale *within reason* is safe.
- **Photometric** (appearance): brightness/contrast/hue jitter, small Gaussian blur (simulates defocus), noise. Careful: too-aggressive hue jitter on skin-color-dependent tasks (hand segmentation!) can break the color cues.
- **The "cut" family**: Cutout/Random Erasing (zero out random patches — forces the model off shortcut textures), MixUp (blend images+labels), CutMix (patch replacement). Effective on CNNs, now standard in modern training recipes.

## The framework view

- **Albumentations** (fast, GPU-friendly, the defacto library), `torchvision.transforms` (the base), and `torchvision.transforms.v2` (tensor-native, functional). Order matters: geometric before tensor ops, then to-tensor, normalize.
- **Test-time augmentation (TTA)**: at inference, average predictions over flipped/cropped variants — a "free" accuracy boost at N× the inference cost.

## The two failure modes I keep hitting

1. **Over-augmentation**: the train set looks unlike real data → the model learns "blurred/rotated" artifacts instead of the class. Sanity check: view your augmented batch (that's also how you notice broken label alignment with MixUp).
2. **Semantic leakage**: a transform that *changes the label*. E.g. cropping a gesture dataset so the hand's identity changes; rotating digits so 6↔9 flip (for landmark systems, geometric transforms must not break the keypoint↔image correspondence).

## For landmark/gesture pipelines specifically

- Landmark models train on **keypoint-preserving** transforms: the transform must be applied to the *keypoint coordinates* too (flip the x of landmarks on horizontal flip, rotate the landmarks with the image). A mismatch here silently corrupts the labels — the #1 landmark-training bug (see [[pose-landmark-estimation]]).
- Synthetic backgrounds / chroma-keyed backgrounds for hand segmentation, cutout-style hand occlusion (fingers behind objects) — targeted augmentation for the exact failure modes in [[report]].

## Related

- [[deeplearning/regularization-normalization]] — why augmentation = regularization.
- [[pose-landmark-estimation]] — keypoint-aware augmentation.
- [[hestreg]] — the gesture project that needs these recipes.
- [[deeplearning/model-optimization-for-inference]] — TTA at the edge is a latency decision.
