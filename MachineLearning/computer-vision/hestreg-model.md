---
id: hestreg-model
aliases: []
tags: []
---

# Hestreg Model

The ML model powering [hestreg](hestreg.md) — the hand gesture recognition system. Given a processed hand image, it outputs a gesture class.

## What the model does

- **Input**: preprocessed hand frame (segmented, background removed — see the HAND SEGMENTATION section in [hestreg](hestreg.md)), typically 224×224 RGB.
- **Output**: a class label for the gesture (via [softmax-function](../mathematical-function/softmax-function.md) over gesture classes).

## Architecture choice

The classic approach for this kind of task is a **CNN backbone** (see [intro-to-convolutional-neural-networks](../deeplearning/intro-to-convolutional-neural-networks.md)) with a small classifier head. Two flavors:

1. **From scratch CNN** — learn everything from the gesture dataset. Needs lots of data; see [hand-gesture-recognition](hand-gesture-recognition.md) for the pipeline (palm detection → landmarks → classification).
2. **Transfer learning** (recommended) — take a pretrained backbone (MobileNet for edge, ResNet for accuracy) and fine-tune the head. Much less data needed — see [transfer-learning](../algorithms/transfer-learning.md).

## Training details worth remembering

- **Preprocessing**: resize, normalize (mean/std), augment (rotation, flip, brightness) — augmentation is cheap accuracy.
- **Data split**: stratified train/val/test by subject or by gesture *type* to avoid leakage.
- **Loss**: cross-entropy (log loss) — see [loss-function](../algorithms/loss-function.md).
- **Optimizer**: AdamW + [how-to-use-lr-scheduler](../deeplearning/how-to-use-lr-scheduler.md) (cosine or ReduceLROnPlateau) — see [optimizers](../deeplearning/optimizers.md).
- **Metrics**: per-class accuracy/precision/recall (some gestures are harder), not just overall accuracy.

## Notes

- The report for the project lives in [report](report.md).
- Deployment: the MODEL MANAGER in [hestreg](hestreg.md) should just load pretrained weights — no training UI.
