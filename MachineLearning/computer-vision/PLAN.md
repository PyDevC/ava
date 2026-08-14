# computer-vision — Plan

## Goal of this directory

Document the **hestreg hand-gesture-recognition project** and the CV knowledge needed to build it: the system architecture, the detection/landmark pipeline, and the classification model. The goal is a complete, self-contained project write-up plus reusable CV notes.

## What's already here

- [[hestreg]] — the system design: sessions, accelerator, model manager, hand segmentation, gesture recognition, control unit.
- [[hestreg-model]] — the ML model: CNN backbone, transfer learning, training details.
- [[hand-gesture-recognition]] — the pipeline: palm detection → landmarks → gesture classification, dynamic gestures.
- [[report]] — the project report, with review notes on what's missing.
- [[report-completion]] — the tracked plan for filling the report's data gaps.
- Reusable CV knowledge: [[object-detection]], [[segmentation]], [[pose-landmark-estimation]], [[data-augmentation]]

## What to add next

- [x] **Fill the report gaps** (already flagged in [[report]]):
  - [x] Dataset + metrics (per-gesture precision/recall) + latency numbers → tracked in [[report-completion]]
  - [x] Baselines: static vs dynamic models, CNN from scratch vs MobileNet fine-tune → tracked in [[report-completion]]
  - [x] Failure modes: occlusion, motion blur, gesture ambiguity → tracked in [[report-completion]]
- [x] **Reusable CV knowledge** (beyond this one project):
  - [x] Object detection overview (YOLO, RetinaNet, one-stage vs two-stage)
  - [x] Segmentation: U-Net, semantic vs instance (relevant to the "hand segmentation" module)
  - [x] Pose/landmark estimation (MediaPipe, the 21-point hand model) — deep dive
  - [x] Data augmentation for images (rotate/flip/hue) — practical recipes
  - [ ] Model deployment on edge/low-latency (MobileNet, quantization) — ties to [[../deeplearning]] inference notes → covered in [[../deeplearning/model-optimization-for-inference]]
- [ ] **Pipeline details**:
  - [ ] Background removal / segmentation methods comparison (thresholding vs model-based) → covered in [[segmentation]]
  - [ ] Real-time processing: frame rate, batching, threading (extends [[hestreg]]'s session notes)

## Prioritization

1. **Report completion** — finish what the project document says it needs.
2. **Landmark estimation + segmentation** — the two techniques the system actually rests on.
3. **Detection overview + augmentation** — general CV tools I'll reuse.
