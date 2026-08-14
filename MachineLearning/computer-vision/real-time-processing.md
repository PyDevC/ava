# Real-time processing

Real-time for hestreg means: **the pipeline must finish within the frame period** — every frame captured by the camera has to be processed before the next one arrives, or the system lags and gestures feel broken. This note is the "session" layer of [hestreg](hestreg.md) seen from a timing budget.

## Frame-rate budgeting

Camera capture rates are typically 30 FPS (webcam) or 60 FPS (gaming cams):

```
budget = 1000 ms / FPS
30 FPS → 33 ms per frame   (capture + preprocess + model + control)
60 FPS → 16 ms per frame
```

Measure in **ms per stage**, not "FPS of the model". The full pipeline — frame grab, hand detection, segmentation/background removal, landmark extraction, gesture classification (see [hand-gesture-recognition](hand-gesture-recognition.md)) — each has its own cost, and they add up. Profile each stage once; the budget decides what can stay and what must be optimized.

## Latency vs throughput

- **Throughput** is frames processed per second when you run back-to-back (batching helps).
- **Latency** is the time from one frame *arriving* to its action *emitted* — what the user actually feels. You can have high throughput and still feel laggy if latency is high (queues, jitter).
- For gesture control, target **per-frame latency under the budget**, not "FPS in a benchmark". Pipeline latency = capture wait + infer time + control time.

## Batching and threading

The classic structure is **two threads with a queue**:

```
capture thread:  camera → queue (always busy grabbing the next frame)
inference thread: dequeue → process → control (always busy computing)
```

The capture thread and inference thread run concurrently — while the model computes on frame N, the camera grabs frame N+1. That's what lets a 50 ms model run at camera speed. The queue depth is the latency/throughput knob: a deep queue smooths jitter but adds lag (the user sees gestures "behind"). Double-buffering (2 slots) is the usual sweet spot.

**Batching** is less useful here than in offline inference: a batch of 2–4 frames can improve GPU utilization but adds latency — the batch waits for frames to fill. For a single-camera interactive system, single-frame with a good model usually wins.

## The cheap wins first

- **Downsampling**: hand classification needs a small input (e.g. 128×128 or even 64×64 for the classification net). Detection/landmarks can run on a downscaled frame too. Every dimension halving cuts input pixels 4×.
- **Model size vs speed**: a small CNN from scratch beats MobileNet on latency; MobileNet beats it on accuracy-per-flop (see [hestreg-model](hestreg-model.md)). The tradeoff is real: measure both on your actual hardware (edge vs desktop GPU) before choosing.
- **Quantization**: INT8 post-training quantization is the single biggest free speedup on edge (see [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md)). FP16 on GPU comes for free.
- Skip heavy work on unchanged frames (temporal reuse), run the segmentation at lower resolution than the classification.

## Why it matters

Real-time processing is what separates a demo from a usable product: a 90%-accurate model at 5 FPS feels worse than an 80%-accurate one at 30 FPS, because the user perceives delay, not accuracy. The report's latency numbers ([report](report.md)) should be measured per stage with this budget in mind.

## Related

- [hestreg](hestreg.md) — the system these timings apply to (sessions, accelerator).
- [segmentation](segmentation.md) — the background-removal stage, a per-frame compute cost.
- [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md) — quantization/pruning to buy back budget.
- [report](report.md) — where the latency numbers get reported.
