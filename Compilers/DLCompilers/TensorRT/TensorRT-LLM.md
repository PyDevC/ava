# TensorRT-LLM vs vanilla TensorRT

Two products, one name, different jobs. Vanilla TensorRT is the general DNN engine compiler; TensorRT-LLM (TRT-LLM) is a whole LLM serving toolkit built around it. I reach for different ones depending on the model.

## Vanilla TensorRT

- General deep learning inference compiler: any model (conv/attention/MLP) from ONNX → engine (see [Engine build](TensorRT-Engine-Build.md)).
- Optimizes *static graph execution*: fusion, tactics, precision, memory.
- Best for **CNNs and non-transformer workloads**, and for transformer inference where I control shapes and don't need serving features.

## TensorRT-LLM

Purpose-built for **serving LLMs at scale** (Llama, Mistral, Qwen, etc.). It has its own engine builder, but the value is in the serving features vanilla doesn't have:

- **In-flight batching** (continuous batching): batches are formed per *iteration*, not per *request* — finished sequences drop out and new ones join immediately. Vanilla TensorRT batches at request level and wastes GPU on finished/padded sequences.
- **Paged KV cache**: KV cache lives in pages, allocated on demand, like virtual memory — no wasted pre-allocated context for varying sequence lengths.
- **Quantized model support**: GPTQ/AWQ/FP8/SmoothQuant etc. — loads the quantized weights *directly* and runs the kernels for them (vanilla needs a calibration pipeline, see [Precision calibration](TensorRT-Precision-Calibration.md)).
- **Engine building for transformers**: `trtllm-build` takes a HuggingFace-style model dir (or a converted checkpoint) and produces an engine, with attention variants (FlashAttention, in-flight batching), context/decoder split, etc.
- Streaming, speculative decoding, LoRA swapping, chunked prefill.

## When to reach for which

- **Transformer / LLM serving** (online, mixed lengths, GPU-flooded): TRT-LLM — you'd have to reimplement continuous batching + paged KV yourself on vanilla.
- **CNN / generic model, or a fixed-shape transformer** you just want fast and simple: vanilla TensorRT — fewer moving parts, engine + trtexec is one command.
- Rough line: serving LLMs → TRT-LLM; everything else → vanilla. When unsure, benchmark vanilla first (it's simpler), and only move to TRT-LLM when batching/quantization features are the bottleneck.

## Why it matters

Both claim "TensorRT", and grabbing the wrong one wastes a week: TRT-LLM for a ResNet is absurd overkill; vanilla TRT for serving Llama leaves 2-4x on the table. The names are the trap.

## Related

- [TensorRT](TensorRT.md) — the umbrella.
- [Engine build](TensorRT-Engine-Build.md) — vanilla's flow.
- [Optimization profiles](TensorRT-Optimization-Profiles.md) — the dynamic-shape answer in vanilla.
- [Comparison-Matrix](../Comparison-Matrix.md) — where both sit vs IREE/Inductor.
