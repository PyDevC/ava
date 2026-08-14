# Report completion notes

The [[report]] review flagged gaps. This note tracks the **data-driven completion** of the hestreg write-up: real numbers, real baselines, real failure modes. (Checkboxes here mirror the report's requirements — see the report for the prose.)

## Dataset + metrics

- [ ] **Per-gesture precision/recall** — not just accuracy. Gesture classes are imbalanced (see [[algorithms/imbalanced-classification]]); report the confusion matrix + per-class P/R + macro-F1.
- [ ] **Latency numbers** — per-stage: palm detection ms, landmark ms, gesture classify ms, total pipeline ms, and frames-per-second at the *target device* (the edge hardware the accelerator discussion in [[hestreg]] is about). Measure with warm cache, p50/p95.
- [ ] Dataset description: #samples per gesture, subjects, camera/lighting variation, train/val/test split discipline (see [[algorithms/cross-validation]]).

## Baselines

- [ ] **Static vs dynamic**: frame-level (static) classifier vs sequence model over N frames (dynamic gestures). Expected: static simpler+cheaper; dynamic needed for temporal gestures (see [[hand-gesture-recognition]]).
- [ ] **CNN from scratch vs MobileNet fine-tune**: the transfer-learning comparison (see [[hestreg-model]] and [[algorithms/transfer-learning]]). The fine-tune should win on small data; quantify the gap.
- [ ] **Landmark-based vs raw-image classifier**: the pipeline's core design choice (see [[hand-gesture-recognition]]) — does going through 21 landmarks actually beat feeding the crop straight to a CNN? That comparison is *the* justification for the architecture.

## Failure modes

- [ ] **Occlusion**: hand behind objects / fingers together — quantify per-gesture degradation (see [[pose-landmark-estimation]]).
- [ ] **Motion blur**: fast gestures → landmark jitter → classification flip. Measure at increasing hand speed.
- [ ] **Gesture ambiguity**: similar shapes (fist vs open-palm at different angles), lighting changes, skin-tone variation.

Each failure mode needs: a reproduction, the measured impact, and the mitigation that went into the system (augmentation [[data-augmentation]], post-processing, fallback).

## Related

- [[report]] — the document these numbers fill in.
- [[hestreg]] / [[hestreg-model]] / [[hand-gesture-recognition]] — the system and model the numbers describe.
- [[algorithms/model-interpretability]] — how to present the eval honestly.
