# Mixed precision training

Mixed precision trains with **half precision (FP16/BF16)** where possible and **FP32** for the parts that need it — getting ~2x throughput and half the memory on tensor-core GPUs (see [deep-learning-hardware](../../Store/deep-learning-hardware.md)).

## Why it works

- Tensor cores (Volta+) do FP16 matmuls at 2x (or more) the FP32 rate.
- Half the bytes = half the memory and half the bandwidth pressure.
- But FP16 has a tiny dynamic range (max ~65504, and denormals below ~1e-4) — naive FP16 training overflows/underflows gradients.

## The three-part recipe (NVIDIA's "AMP")

1. **Master weights in FP32** — updates accumulate in full precision; only the *copy* used in forward/backward is FP16.
2. **Loss scaling** — multiply loss by a big constant (e.g. 2^11) before backward so small gradients don't underflow to 0 in FP16; divide them back before the update. Dynamic loss scaling auto-adjusts when inf/nan appears.
3. **FP32 for sensitive ops** — reductions, losses, softmax, exp stay FP32 (this is exactly the "keep it in log/FP32" theme of [exp-ln-logspace](../mathematical-function/exp-ln-logspace.md)).

## BF16 vs FP16

- **FP16**: 1 sign, 5 exponent, 10 mantissa bits. Needs loss scaling.
- **BF16**: 1 sign, 8 exponent, 7 mantissa — *same exponent range as FP32*, so **no loss scaling needed**. Lower precision mantissa, but hugely more robust to over/underflow. The default for LLM training (NVIDIA Ampere+, AMD MI200+).
- PyTorch: `torch.autocast("cuda")` + `torch.cuda.amp` — autocast does the cast-insertion for you.

## What actually changes in code

```python
with torch.autocast(device_type="cuda", dtype=torch.float16):
    loss = loss_fn(model(x), y)      # fp16 matmuls, fp32 reductions
scaler = torch.cuda.amp.GradScaler()
scaler.scale(loss).backward()
scaler.step(optimizer)
scaler.update()
```

(The `torch.cuda.amp.GradScaler` path; for BF16, autocast alone usually suffices.)

## Interaction with the compiler story

`torch.compile` handles autocast itself (the graph is compiled for the mixed-precision types). Inductor also does **automatic mixed precision + FP16/BF16 kernel generation** when the input dtype allows (see [TorchInductor](../../Compilers/DLCompilers/TorchInductor/TorchInductor.md)).

## Related

- [optimizers](optimizers.md) — the performance chapter this belongs to.
- [distributed-training](distributed-training.md) — mixed precision + FSDP is the standard huge-model stack.
- [deep-learning-hardware](../../Store/deep-learning-hardware.md) — the tensor cores it exploits.
