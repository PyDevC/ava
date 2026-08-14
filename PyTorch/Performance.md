# PyTorch performance and memory

The everyday performance toolbox: profile first, then remove the real bottleneck — usually Python/launch overhead, memory churn, or data loading. These notes assume the compiler side is already covered (see [PLAN](../Compilers/PyTorchCompiler/)).

## Profiling: `torch.profiler`

```python
with torch.profiler.profile(activities=[torch.profiler.ProfilerActivity.CUDA],
                            record_shapes=True, profile_memory=True) as prof:
    model(x)
print(prof.key_averages().table(sort_by="cuda_time_total", row_limit=20))
```

- Read the **kernel-level** table: per-op time, CUDA time, #calls. Small-op-count + huge-call-count → launch-bound → `torch.compile` territory.
- `with_stack=True` shows the Python call site (finding *which* line launched the kernel).
- `torch.profiler.schedule` for multi-step profiles; `--with-module` view on Hugging Face models.
- The classic triage: CUDA time >> CPU time → compute/memory-bound kernels; CPU >> CUDA → launch/Python overhead → fuse (compile), reduce launches (chunked ops), CUDA graphs.

## The caching allocator

- PyTorch **caches freed GPU memory** (doesn't return to the driver every time) → allocation is cheap, but the cache grows to the high-water mark and *appears* as a leak (`torch.cuda.empty_cache()` reclaims it to the driver; `torch.cuda.memory_summary()` shows the truth).
- **Peak-memory reduction order**: `torch.compile` (fuses, reuses buffers) > gradient accumulation / checkpointing (`torch.utils.checkpoint` trades compute for memory) > mixed precision ([mixed-precision](../MachineLearning/deeplearning/mixed-precision.md)) > FSDP (shards optimizer states, [distributed-training](../MachineLearning/deeplearning/distributed-training.md)).
- `PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True` (or `max_split_size_mb`) can cut fragmentation spikes on big-tensor workloads.

## no_grad vs inference_mode

- `torch.no_grad()`: disables the Autograd **dispatch key** (no graph recorded), but still tracks `requires_grad` metadata and (with grad tensors) can be slow.
- `torch.inference_mode()`: the *stronger* one — no autograd at all, tensors become "inference-only" (`inference` dispatch key), allows bigger fusion/optimization headroom. Use for pure inference; assert-free.
- `model.eval()` ≠ `no_grad` — eval changes *layers* (dropout/BN, see [regularization-normalization](../MachineLearning/deeplearning/regularization-normalization.md)), `no_grad` changes the *runtime*. You need both at inference.

## Data loading

- `DataLoader(num_workers>0, pin_memory=True)` — overlap CPU prep with GPU compute. The real rule: **the GPU should never wait on Python**.
- Bottlenecks: single-threaded `Dataset.__getitem__` doing transforms → move transforms to workers, or (fastest) do them on GPU/in a separate preprocessing stage.
- `prefetch_factor`, `persistent_workers`, `non_blocking` `.to(device)` for pin_memory copies.
- Measure: `torch.profiler` shows data-loading gaps; a simple timer around the training step is the honest check.

## Related

- [PLAN](../Compilers/PyTorchCompiler/) — `torch.compile` as the first "make it faster" lever.
- [mixed-precision](../MachineLearning/deeplearning/mixed-precision.md) — the 2x-4x knob.
- [distributed-training](../MachineLearning/deeplearning/distributed-training.md) — beyond one GPU.
- [Explore](Explore.md) — the repo layout these subsystems live in.
