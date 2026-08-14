# Optimization profiles + dynamic shapes in TensorRT

TensorRT prefers static shapes: the builder picks the fastest kernel for *exact* sizes. When inputs vary (variable batch, sequence length, image sizes) you get dynamic shapes, and TensorRT's answer is **optimization profiles**.

## What a profile is

A profile declares, per input tensor, three shapes:

- **min** — smallest shape the engine must handle.
- **opt** — the shape the builder optimizes *for* (this is where tactic selection is best).
- **max** — largest shape.

```bash
trtexec --onnx=model.onnx --saveEngine=model.engine \
  --minShapes=input:1x3x224x224 \
  --optShapes=input:8x3x224x224 \
  --maxShapes=input:32x3x224x224
```

The builder then picks kernels that are valid across the whole range, tuned near opt. Shapes outside [min, max] are rejected at runtime.

## Multiple profiles

You can add several profiles (one per `IOptimizationProfile` in the Python API):

```python
profile = builder.create_optimization_profile()
profile.set_shape("input", (1,3,224,224), (8,3,224,224), (32,3,224,224))
config.add_optimization_profile(profile)
```

Use more than one when your workloads have *distinct* regimes — e.g. profile A tuned for batch 1 (latency, online) and profile B tuned for batch 32 (throughput, offline). Each profile is a separate kernel set; at runtime you pick the profile per call.

## Bindings at inference time

Dynamic input tensors must have their shape **set per call**, and outputs sized accordingly:

```python
context.set_input_shape("input", (16,3,224,224))     # within the active profile
context.set_tensor_address("input", in_ptr)          # CUDA buffers
context.set_tensor_address("output", out_ptr)
context.execute_async_v2([], stream)                 # shapes/addresses already set
```

Setting a shape that violates the profile bounds errors out. The context allocates activations for the max shapes, so memory is sized by max, not by what you actually send.

## Workspace + tactics

The builder explores **tactics** (candidate CUDA kernels per layer) within a **workspace** budget:

- `--workspace=N` / `config.set_memory_pool_limit(WORKSPACE, N)` — cap on scratch memory the tactics may use. More workspace → more/wider tactics → better kernels, but more memory.
- Each tactic is benchmarked on the exact GPU; with profiles, the benchmark happens over the min/opt/max range — a tactic must be valid everywhere in the range to be chosen.
- `--verbose` shows the chosen tactic per layer — the debugging view when a layer underperforms.

## Why it matters

Dynamic shapes + TensorRT = the profile skill. Get the opt shapes right (real deployment shapes, not guesses) or you leave performance on the table; get min/max wrong and inference errors at runtime. It's the #1 production issue with TensorRT engines.

## Related

- [Engine build](TensorRT-Engine-Build.md) — where profiles are declared.
- [TensorRT](TensorRT.md) — overview (static vs dynamic).
