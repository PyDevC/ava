# Segmentation: semantic and instance

Segmentation labels **every pixel**: semantic (class per pixel, no instances) vs instance (each *object* gets its own id). The system's "hand segmentation" module is a semantic-segmentation task on a narrow class set (see [hestreg](hestreg.md)).

## Semantic segmentation — U-Net and friends

**U-Net** (2015): the canonical architecture — an **encoder (downsampling) + decoder (upsampling) with skip connections**:

```
encoder: C3→C4→C5→C6   (feature maps shrink)
decoder: U6→U5→U4→U3   (features upsampled)
skip:    concatenate encoder features at same scale → fine detail preserved
```

- The skips are the key idea: upsampling alone gives blurry blobs; the skip connections restore the spatial detail lost to downsampling. The output is a per-pixel class map.
- **Fully-convolutional** (FCN): any network whose output is a dense pixel map — U-Net, DeepLab (atrous/dilated convolutions for multi-scale context), PSPNet.
- **Loss**: per-pixel cross-entropy, or **Dice loss** (`1 - 2|P∩T|/(|P|+|T|)`) for class imbalance — very relevant when "hand vs background" is 1:100 (see [imbalanced-classification](../algorithms/imbalanced-classification.md)).

## Instance segmentation — the harder problem

Semantic = "which class, per pixel". Instance = "which *object*" (two people get different labels). Approaches:

- **Mask R-CNN**: Faster R-CNN ([object-detection](object-detection.md)) + a mask head per proposal — the accurate standard.
- **YOLACT / SOLO / BlendMask**: one-stage instance segmentation (speed).
- **Panoptic segmentation**: semantic + instance unified into one labeling (stuff classes semantic, things instances).

## The background-removal angle

For "hand segmentation" the practical question is usually **foreground/background**:

- **Thresholding** (color/hue range, depth threshold): free, fast, brittle (lighting/skin-tone).
- **Model-based** (U-Net/MobileNet-style binary segmenter): robust, needs training data, runs on every frame — the compute decision matters on edge (see [hestreg](hestreg.md)'s accelerator discussion).
- **MediaPipe/GPU tricks** (selfie-segmentation, grabcut): the pragmatic middle ground.

## Related

- [hand-gesture-recognition](hand-gesture-recognition.md) — where segmentation sits in the pipeline.
- [object-detection](object-detection.md) — Mask R-CNN's parent.
- [intro-to-convolutional-neural-networks](../deeplearning/intro-to-convolutional-neural-networks.md) — the encoder backbone.
- [imbalanced-classification](../algorithms/imbalanced-classification.md) — why Dice loss.
