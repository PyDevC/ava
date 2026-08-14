# Model optimization for inference

After training, the model must run **fast and small** on deployment hardware. This note covers the four standard techniques. The compiler view of the same problem lives in the [DLCompilers](../../Compilers/DLCompilers/PLAN.md) notes.

## Quantization

Store and compute with fewer bits than FP32:

- **Post-training quantization (PTQ)**: convert weights to INT8 (per-tensor or per-channel), optionally with a calibration set to pick scales. No retraining; small accuracy hit.
- **Quantization-aware training (QAT)**: insert fake-quant ops *during* training so the model learns to tolerate quantization. Best accuracy, costs a training pass.
- Scale/zero-point math: `x_q = round(x/s + z)`; inference ops run in INT8 and dequantize at the end. Needs quantization-friendly ops (see the hard activations note [hard-activations-step](../mathematical-function/hard-activations-step.md) — clamps are free in INT8).
- The compiler angle: frameworks fuse quantize→dequantize into kernels (TensorRT, ONNX Runtime do this automatically — see [TensorRT](../../Compilers/DLCompilers/TensorRT/TensorRT.md)).

## Pruning

Remove weights (unstructured) or entire channels/heads (structured):

- **Unstructured**: zero small-magnitude weights → sparse model; needs sparse-aware hardware/kernels to actually speed up.
- **Structured**: drop whole filters/heads → real speedup on dense hardware, no special kernels.
- **Iterative magnitude pruning** is the standard recipe: train, prune, retrain/fine-tune, repeat.

## Distillation

Train a small **student** to imitate a large **teacher**:

```
L = α·L_task + (1-α)·L_distill(student_logits, teacher_logits)
```

The soft teacher probabilities (temperature-smoothed softmax, see [softmax-function](../mathematical-function/softmax-function.md)) carry more information than hard labels — "dark knowledge" about which wrong answers are close. This is how "tiny" BERT-like models get most of the teacher's quality.

## The practical ordering

1. Measure (profile) — find the actual bottleneck: memory-bound kernels, launch overhead, or compute (see [GraphBreaks](../../Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks.md) for the graph-level view).
2. **torch.compile** first — free speedup, then
3. **Quantization** (INT8/FP16 — see [mixed-precision](mixed-precision.md) for the training-side FP16) then
4. **Pruning/distillation** — bigger surgery, for when you need real size reduction.

## Related

- [TorchInductor](../../Compilers/DLCompilers/TorchInductor/TorchInductor.md) — how `torch.compile` fuses and lowers all of this.
- [mixed-precision](mixed-precision.md) — the FP16/BF16 training-side counterpart.
- [transfer-learning](../algorithms/transfer-learning.md) — distillation is a sibling "knowledge transfer".
