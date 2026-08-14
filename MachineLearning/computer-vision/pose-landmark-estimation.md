# Pose and landmark estimation

Pose estimation predicts **keypoints** on a person (joints) or on a part (hand: the 21-point model) from an image. This is the core of the [hand-gesture-recognition](hand-gesture-recognition.md) pipeline: palm detection → 21 landmarks → gesture classification.

## The two tasks

- **2D pose** (person: 17–25 joints; hand: 21 landmarks): locate points in image space. Per-keypoint: a (x, y, confidence) tuple.
- **3D pose**: predict (x, y, z) — either from the image directly or by lifting 2D predictions. For hands, the 3D shape of the 21 points is what makes gestures robust to viewpoint.

## How it's done (MediaPipe hand, the reference)

MediaPipe's hand model is a **two-stage pipeline**:

1. **Palm detector** (a fast detector, see [object-detection](object-detection.md)) — palms are easy to detect (rigid, small rotation); the hand *itself* has huge articulation variance, so detecting the palm first is the trick.
2. **Hand landmark model** — takes the cropped palm region, regresses 21 3D landmarks with per-point visibility. Trained to predict 3D coordinates (z in a local frame), which is why the output includes depth-like values.

MediaPipe also has pose (33 points, for the body) and face landmarks (468 points) — same design: detect region → regress landmarks.

## Landmark → semantics

The 21 hand landmarks have a fixed ordering (wrist, then 4 fingers × 4 joints, thumb=2). Downstream logic (like [hestreg](hestreg.md)'s gesture classification) consumes the *relative geometry*: finger *states* (extended/curled) via inter-joint angles, or the landmark *coordinates* fed to a small classifier. This is where the "gesture recognition" module comes from — see [hand-gesture-recognition](hand-gesture-recognition.md).

## Keypoint models — the big picture

- **Heatmap-based** (the accurate classic): the network predicts a *heatmap* per keypoint (a soft peak where the joint is); argmax + refine. Slow-ish but very accurate — the two-stage/HRNet style.
- **Regression-based** (the fast/modern): directly regress coordinates (MediaPipe, YOLOv8-pose). Faster, more robust to cropping, and the coordinate output feeds classifiers trivially.
- **3D**: either direct (from image) or "2D lift" (2D → 3D network). Hands favor direct 3D regression (MediaPipe does this).

## The robustness reality

Landmarks are *only* as good as detection + lighting + occlusion. Gesture systems fail on: occlusion (fingers behind the hand), motion blur (fast gestures), and ambiguous geometry (fist vs open palm closeups). See the failure-mode discussion in [report](report.md) and [hand-gesture-recognition](hand-gesture-recognition.md).

## Related

- [hand-gesture-recognition](hand-gesture-recognition.md) — the pipeline that consumes landmarks.
- [object-detection](object-detection.md) — the palm detector stage.
- [hestreg](hestreg.md) — the system integration.
- [data-augmentation](data-augmentation.md) — what makes the landmark model robust.
