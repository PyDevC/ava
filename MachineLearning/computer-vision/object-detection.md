# Object detection overview

Detection answers "what objects are where" — a box (or mask) around each instance plus a class. The modern landscape: **one-stage** (fast, direct) vs **two-stage** (accurate, proposals-then-refine), and the anchor-free/transformer variants that now dominate.

## Two-stage detectors (the accurate baseline)

1. **Region proposal**: propose candidate boxes (class-agnostic "is there *something* here").
2. **Classify + refine**: for each proposal, classify and adjust the box.

- **R-CNN** (2014): selective search proposals + CNN per proposal (slow — thousands of forward passes).
- **Fast R-CNN**: share the backbone conv features; pool per-proposal (RoI pooling) — one backbone pass.
- **Faster R-CNN**: replace selective search with a learned **RPN** (Region Proposal Network) — the "fast" line ends here and it's still the accuracy default. FPN (feature pyramid) heads handle small objects.

Cost: two stages = more compute at inference than one-stage. The accuracy lead has since eroded.

## One-stage detectors (the speed/elegance standard)

Predict boxes + classes **directly** on a grid of anchor locations:

- **YOLO** (You Only Look Once, 2016→ now YOLOv8/v9/v10): divide image into cells, each predicts boxes+scores+classes. One pass, extremely fast — the edge/realtime standard (and what a palm-detection system like [hestreg](hestreg.md)'s uses).
- **RetinaNet**: one-stage with **focal loss** — fixes the "class imbalance drowns the loss" problem that made early one-stage models miss (see [imbalanced-classification](../algorithms/imbalanced-classification.md)). This closed the accuracy gap to two-stage.
- **Anchor-free** (CenterNet, FCOS, CornerNet): predict object *centers* + sizes instead of pre-defined anchors — simpler, and now the default design (YOLOv8 is anchor-free).

## The modern entries

- **DETR / deformable DETR**: transformers ([Transformers](../deeplearning/Transformers.md)) treat detection as a *set-prediction* problem (no anchors, no NMS!). Elegant, but slower to converge.
- The practical current choice: **YOLO family** for speed/edge, **Faster R-CNN/RetinaNet** for accuracy-focused pipelines, **DETR** for research.

## The shared machinery

- **NMS (non-max suppression)**: overlapping boxes for the same object → keep the max-score, suppress the rest. Still the postprocessing everywhere (DETR avoids it).
- **mAP**: the metric — precision/recall across IoU thresholds (see [model-interpretability](../algorithms/model-interpretability.md) for eval thinking).
- Detection→tracking→pose is the *pipeline* pattern ([hand-gesture-recognition](hand-gesture-recognition.md)): detect the region of interest, then run a lighter model on the crop.

## Related

- [hand-gesture-recognition](hand-gesture-recognition.md) — detection as the first stage of the pipeline.
- [intro-to-convolutional-neural-networks](../deeplearning/intro-to-convolutional-neural-networks.md) — the backbone.
- [imbalanced-classification](../algorithms/imbalanced-classification.md) — the focal-loss motivation.
- [model-interpretability](../algorithms/model-interpretability.md) — the mAP metric.
