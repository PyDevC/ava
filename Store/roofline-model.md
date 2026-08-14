# Roofline and the bandwidth-vs-compute model

The mental model for "why is my workload memory-bound or compute-bound" — the model that explains GPU behavior across every workload in this repo (LLM inference, fusion decisions, quantization wins).

## The roofline model

Two limits on any kernel:

```
compute bound:  runtime = FLOPs / peak_FLOPS
memory bound:   runtime = bytes / peak_bandwidth
```

Plot runtime vs arithmetic intensity (FLOPs/byte) → an L-shape: below the knee you're **memory-bound**, above it **compute-bound**. The peak compute rate is the flat roof; the bandwidth slope is the rising edge.

## The LLM inference case (why it matters)

Decoding is **memory-bound by the weights**:

- 7B model at FP16 = 14GB of weights. At H100's ~3.35TB/s: ~4ms to stream all weights once per token — *regardless* of how fast the math is. GPU utilization while decoding is famously low (5–15%) precisely because bandwidth saturates first.
- The fixes are all "reduce bytes": **quantization** (FP16→INT8→FP4 shrinks the weight bytes, [[MachineLearning/deeplearning/model-optimization-for-inference]]), KV-cache management (the activations you also stream), and **batching** (share the weights across many requests — the weights are read once per batch, so batches amortize the bandwidth).

## Where the model bites in this repo

- **Fusion** ([[Compilers/PyTorchCompiler/TorchInductor/Fusion-Scheduler]]): fusing two kernels saves the *intermediate write+read* (memory traffic) — a memory-bound win. That's why elementwise fusion is nearly always worth it.
- **The `default` vs `max-autotune` decision**: compute-bound GEMMs reward autotuned tile sizes (compute roof); memory-bound pointwise ops don't care (bandwidth slope).
- **Channels-last / memory layout** ([[PyTorch/Tensor-TensorImpl-Storage]]): layout changes *locality*, i.e. effective bandwidth.
- **Quantization** ([[MachineLearning/deeplearning/model-optimization-for-inference]]): cutting bytes IS cutting time on the memory-bound slope — the exact reason it wins so reliably on LLMs.

## The practical reflex

For any perf question: **count the bytes**. Is the data bigger than the compute is slow? If yes → memory-bound → reduce bytes (quantize, fuse, better layout). If no → compute-bound → improve the math (tiles, tensor cores, precision). The answer "it's memory-bound" is usually the *interesting* answer.

## Related

- [[deep-learning-hardware]] — the hardware numbers the model uses.
- [[gpu-spec-catalog]] — bandwidth vs FLOPs per generation.
- [[MachineLearning/deeplearning/model-optimization-for-inference]] — quantization as a bandwidth fix.
- [[PyTorch/Performance]] — the measurement toolkit.
