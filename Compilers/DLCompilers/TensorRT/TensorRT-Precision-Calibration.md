# INT8 precision calibration in TensorRT

The hard part of INT8 quantization isn't the math — it's **knowing the range of activations**. If a layer's activations reach 10.0 and I quantize to 8 bits assuming a max of 1.0, everything above 1.0 clips. Calibration is: measure real activation ranges on representative data, then pick scales that minimize the damage.

## FP16 vs INT8

- **FP16**: no calibration. FP32 → FP16 is a change in mantissa bits (rounding); every value converts cleanly. It's a *precision* change, not a *quantization* change — TensorRT just enables the FP16 kernels and it works.
- **INT8**: a 4x compression of activations/weights with non-linear error behavior. You must provide **scales** (multipliers mapping float ↔ int8) per layer/tensor, computed from observed ranges. No scales → garbage or clipped outputs.

## The calibration flow

1. **Collect a calibration set**: a representative sample of real inputs (hundreds to thousands — enough to cover the ranges the model sees). Not training data, not random noise — *representative* input.
2. **Run the network in FP32, logging activations**: the `Int8Calibrator` does this — TensorRT runs the layers, records min/max of each activation tensor, and picks a scale so the range maps onto [-127, 127].
3. **Choose how the range becomes a scale**:
   - **per-tensor**: one scale for the whole tensor. Cheapest, crudest — a few outlier values stretch the range and everything else loses precision.
   - **per-channel**: a scale per output channel of a conv/matmul. Much better accuracy for weights (each channel has its own magnitude), slightly more kernel work.
4. TensorRT applies the scales and builds INT8 kernels; sensitive layers stay in FP32/FP16 (mixed precision).

## Sensitivity analysis

Calibration picks scales, but **sensitivity analysis** is the accuracy-side tool: measure how much each layer's INT8 quantization degrades the final metric (logit/accuracy delta). The output is a per-layer ranking of "how important is this layer to keep in high precision". Do this when INT8 drops accuracy: find the few high-sensitivity layers, keep them FP16, re-quantize the rest.

## trtexec / API

```bash
trtexec --onnx=model.onnx --int8 --calib=calib_cache.bin --saveEngine=model.engine
```

or the calibrator class:

```python
class Calib(trt.IInt8Calibrator):     # provide batch stream + scales
    ...
config.set_flag(trt.BuilderFlag.INT8)
config.int8_calibrator = Calib(...)
```

## Why it matters

INT8 is where TensorRT earns its 2-4x over FP16, and it's the one step that can silently destroy accuracy. The whole skill: good calibration set + per-channel scales + sensitivity-guided mixed precision. And always re-validate the INT8 engine's accuracy against FP32 before trusting it.

## Related

- [Engine build](TensorRT-Engine-Build.md) — the flow this plugs into.
- [TensorRT](TensorRT.md) — overview of precision reduction.
