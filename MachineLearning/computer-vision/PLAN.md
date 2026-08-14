# computer-vision — Plan

## Goal of this directory

Document the **hestreg hand-gesture-recognition project** and the CV knowledge needed to build it: the system architecture, the detection/landmark pipeline, and the classification model. The goal is a complete, self-contained project write-up plus reusable CV notes.

## What's already here

- [[hestreg]] — the system design: sessions, accelerator, model manager, hand segmentation, gesture recognition, control unit.
- [[hestreg-model]] — the ML model: CNN backbone, transfer learning, training details.
- [[hand-gesture-recognition]] — the pipeline: palm detection → landmarks → gesture classification, dynamic gestures.
- [[report]] — the project report, with review notes on what's missing.

## What to add next

- [ ] **Fill the report gaps** (already flagged in [[report]]):
  - [ ] Dataset + metrics (per-gesture precision/recall) + latency numbers
  - [ ] Baselines: static vs dynamic models, CNN from scratch vs MobileNet fine-tune
  - [ ] Failure modes: occlusion, motion blur, gesture ambiguity
- [ ] **Reusable CV knowledge** (beyond this one project):
  - [ ] Object detection overview (YOLO, RetinaNet, one-stage vs two-stage)
  - [ ] Segmentation: U-Net, semantic vs instance (relevant to the "hand segmentation" module)
  - [ ] Pose/landmark estimation (MediaPipe, the 21-point hand model) — deep dive
  - [ ] Data augmentation for images (rotate/flip/hue) — practical recipes
  - [ ] Model deployment on edge/low-latency (MobileNet, quantization) — ties to [[../deeplearning]] inference notes
- [ ] **Pipeline details**:
  - [ ] Background removal / segmentation methods comparison (thresholding vs model-based)
  - [ ] Real-time processing: frame rate, batching, threading (extends [[hestreg]]'s session notes)

## Prioritization

1. **Report completion** — finish what the project document says it needs.
2. **Landmark estimation + segmentation** — the two techniques the system actually rests on.
3. **Detection overview + augmentation** — general CV tools I'll reuse.
