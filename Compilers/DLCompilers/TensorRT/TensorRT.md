# TensorRT

TensorRT is NVIDIA's high-performance deep learning inference optimizer and runtime. It takes a trained model, applies a bunch of optimizations, and produces an optimized inference *engine* that runs only on NVIDIA GPUs. It is the standard for production inference on NVIDIA hardware (datacenter, Jetson/embedded, and automotive).

Typical speedups are 2-5x over framework-native inference.

## The workflow

```
Trained model (ONNX / TensorFlow / PyTorch)
        |
        v
Parser  (imports the model into a TensorRT network)
        |
        v
Builder (optimizes the network, applies tactics, calibrates precision)
        |
        v
Engine  (serialized, hardware-specific runtime)
        |
        v
Runtime (executes inference with execution contexts)
```

The four components to remember:

- **Parser**: e.g. the ONNX parser reads an exported model and populates a `network` object.
- **Builder**: takes the network plus configuration and generates the optimized engine. Building is expensive, so you serialize the engine and reuse it (only rebuild when model/platform/config changes).
- **Engine**: does the actual inference; you bind input/output buffers to it.
- **Execution context**: holds the intermediate activation memory during a run.

## What optimizations does it do

- **Layer fusion**: combines layers (e.g. Conv + BN + ReLU) into single kernels, cutting kernel launches and memory traffic.
- **Kernel auto-tuning**: for each layer it benchmarks candidate CUDA kernels ("tactics") on your exact GPU and picks the fastest.
- **Precision reduction / quantization**: FP32, FP16, INT8, FP8. INT8 needs a calibration step (feed representative data to compute per-layer scales). It runs numerically safe layers in low precision and keeps sensitive ones in FP32 (mixed precision).
- **Memory optimization**: reuses buffers, minimizes workspace.
- **Transformer/LLM optimizations**: fused attention, KV cache, MoE support (in newer versions and via TensorRT-LLM).

## Things to keep in mind

- Engines are **GPU-architecture specific** - an engine built for an A100 won't run on an H100; build engines on the exact production GPU.
- Best with **static shapes / batch sizes**. Dynamic shapes are supported via **optimization profiles** where you give MIN/OPT/MAX dims per input; the builder picks kernels valid across that range, and the engine may pick a suboptimal kernel for out-of-profile sizes.
- TensorRT is a *closed* binary SDK (parts are open source: parsers, samples). Engines are opaque blobs.

## TensorRT-LLM

TensorRT-LLM is the LLM-specific layer on top: engine building for transformers, in-flight batching, LoRA swapping, speculative sampling, and post-training quantization for supported model families (Llama, Mistral, Whisper, etc.).

## References

- Docs: https://docs.nvidia.com/deeplearning/tensorrt/
- Getting started / how it works: https://docs.nvidia.com/deeplearning/tensorrt/latest/getting-started/quick-start-runtime-tutorial.html
