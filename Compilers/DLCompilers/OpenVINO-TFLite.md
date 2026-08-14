# OpenVINO and TFLite/XNNPACK

Two deployment-side quick notes to complete the DL compiler landscape (from the parent [PLAN](./)): Intel's OpenVINO and the mobile/TFLite stack. Both are *executor-with-fusion* tools (see [ONNXRuntime](ONNXRuntime.md)'s "compiler vs executor" distinction) tuned for specific hardware.

## OpenVINO (Intel)

- **Input**: ONNX, TF, PyTorch (export → `openvino.convert_model`), OpenCV formats. Emits an **OV IR** (`.xml` + `.bin`) → `Core.compile_model` → executable on **CPU (x86), GPU (Intel iGPU/Arc), NPU** (Meteor Lake+).
- **Optimizations**: graph fusion + layout propagation (NCHW→NHWC), INT8 quantization, and **CPU kernel auto-tuning** for x86 (using AVX-512 etc.). Good default for Intel-only edge/server fleets.
- Best when: Intel CPU inference, no NVIDIA GPU, need a mature free runtime. Its runtime is heavy-ish (a big .so); the "runtime API" is `openvino.runtime.Core`.

## TFLite / XNNPACK (mobile/embedded)

- **TFLite**: TF's model → `.tflite` (flatbuffer with a compact op set) → the TFLite runtime. Optimizations: quantization (post-training INT8, FP16), op fusion, and delegates.
- **Delegates**: XNNPACK (ARM/x86, the default CPU delegate with high-perf fp16/int8 kernels), GPU delegate, NNAPI/Delegate API for vendor NPUs. The delegate is the "codegen" — a hand-written high-performance kernel pack, not generated loops.
- The mobile counterpart of ONNX Runtime: fix the op set + hand-tuned kernels → tiny footprint, predictable perf. `.tflite` (or ORT mobile) is what actually runs on-device for real products.

## The "deployment tier" map

```
PyTorch/ONNX model
 ├─ ORT          → any device, executor + fusion
 ├─ TensorRT     → NVIDIA GPU (heavy fusion + fp8/int8)
 ├─ OpenVINO     → Intel CPU/iGPU/NPU
 └─ TFLite/XNN   → mobile/embedded (ARM), tiny + quantized
```

Pick by **the target hardware**, not by brand: GPUs → TensorRT/Inductor; Intel servers → OpenVINO; phones → TFLite/XNNPACK (or ORT mobile). The ONNX interchange ([ONNXRuntime](ONNXRuntime.md)) is how you stay hardware-agnostic.

## Related

- [ONNXRuntime](ONNXRuntime.md) — the same "executor + fusion" model, hardware-agnostic.
- [Comparison-Matrix](Comparison-Matrix.md) — where these sit in the full table.
- [TensorRT](TensorRT/TensorRT.md) — the NVIDIA counterpart of OpenVINO.
