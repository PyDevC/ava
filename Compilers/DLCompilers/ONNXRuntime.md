# ONNX Runtime

ONNX Runtime (ORT) is the **production executor for ONNX models** — the .py/.onnx → serve path most ML products actually ship. Key distinction: it's a *runtime with a fusion layer*, not a full compiler like TVM/XLA (see [README](README.md) for the spectrum).

## What it does

- **ONNX IR** (Open Neural Network Exchange) is the *interchange format*: an op-set standard that PyTorch/TF export into, decoupled from any one framework. `onnx` graph → ORT's own internal graph.
- **Graph-level optimizations**: constant folding, redundant-op elimination, common-subexpression-like dedup, and **op fusion** (e.g. Conv+BN+ReLU → one fused "ConvRelu" kernel), layout transforms, **quantization** (INT8 static/dynamic via the quantization tools).
- **Executors**: CPU (x86/ARM via oneDNN/XNNPACK-ish kernels), CUDA, TensorRT as a backend plugin, OpenVINO/ROCm/WebGPU/DirectML. A single `.onnx` file runs on almost anything — that's the pitch.
- **Sessions**: `InferenceSession` with per-EP (execution provider) control; per-provider kernels get chosen at session build.

## Compiler vs executor — the key difference

ORT is largely a **graph optimizer + kernel dispatcher**: it fuses and picks kernels, but the kernels themselves are mostly pre-written (cuDNN, oneDNN, its own `ort` kernels) rather than *generated from the graph* like Inductor/TVM do. The gaps:

- No schedule search/autotuning of generated loops (uses vendor libs + fusion instead).
- Less aggressive re-lowering; it doesn't go graph→loop-IR→generated-kernel by default.
- Newer ORT additions (ORT "training", the "compile" path) are blurring this — but the classic picture stands.

## When you reach for it

- Shipping a model to a **different runtime than the training framework** (e.g. train in PyTorch, serve via ORT on Windows Server with DirectML).
- Need **portability + decent performance without a build pipeline**.
- Edge/mobile: `onnxruntime-mobile` (small footprint), quantized models.

The trade: you trade full compile-time freedom (custom kernels, exotic fusion, deep device tuning) for the ONNX ecosystem's reach.

## Related

- [README](README.md) — the pipeline map and where ORT sits.
- [TensorRT](TensorRT/TensorRT.md) — the NVIDIA backend EP (often THE reason to use ORT on GPUs).
- [TorchInductor](TorchInductor/TorchInductor.md) — the "generate everything" alternative.
- [tosa-stablehlo](../MLIR/Dialects/tosa-stablehlo.md) — the modern MLIR-y alternative to ONNX as an interchange.
