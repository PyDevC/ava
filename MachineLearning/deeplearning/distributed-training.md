# Distributed training (DDP / FSDP)

When one GPU can't fit the model (or training is too slow), you distribute. Two fundamentally different problems:

1. **Model too big to fit** → *shard the model* (FSDP, tensor parallelism, ZeRO).
2. **Data too much / too slow** → *replicate and data-parallel* (DDP).

## Data parallelism / DDP

Each worker has a **full copy** of the model, trains on a different batch, then **all-reduces the gradients**:

```
grad_avg = all-reduce(Σ grad_i) / world_size
```

- **DDP** (PyTorch `torch.nn.parallel.DistributedDataParallel`): wraps the model; the backward pass *averages gradients across ranks* via a bucketed all-reduce (NCCL on NVIDIA, RCCL on AMD). Forward is fully parallel — the only sync is gradient exchange.
- **DP** (the old `DataParallel`) is a single-process multi-GPU bottleneck; DDP is the real thing.
- Communication cost: gradients are the size of the model, so it scales poorly once a single batch takes less time than the gradient all-reduce.

## Model sharding / FSDP

**FSDP (Fully Sharded Data Parallel)** shards the model *parameters, gradients, and optimizer states* across ranks (like ZeRO-3):

- Forward: **all-gather** the layer's weights before use, **re-shard** after.
- Backward: gather again for gradients, then reduce-scatter.
- Memory scales with `1/world_size` per rank — that's how you train 7B–70B models on a cluster.

Compare: **tensor parallelism** (shard each layer's weight matrix, split matmuls — used *inside* transformer layers, more communication per step but less total) vs pipeline parallelism (split layers across ranks, sequential).

## The practical stack

- `torch.distributed` init → DDP/FSDP wrapper → data loader with a **different shard per rank** (per-rank seed offset, `DistributedSampler`) → checkpointing (sharded + consolidated).
- The **collectives** are the real primitive: `all-reduce`, `all-gather`, `reduce-scatter` (see [hpc-engineer](../../Roadmaps/hpc-engineer.md) for the MPI connection).
- Ties to the compiler story: gradient reduction is a collective op; kernels and comm overlap is where framework engineering shines (see [PLAN](../../PyTorch/)).

## Related

- [optimizers](optimizers.md) — the single-GPU baseline this scales up.
- [regularization-normalization](regularization-normalization.md) — BatchNorm is not distributed-safe (batch stats differ per rank) → LayerNorm or sync-BN.
- [hpc-engineer](../../Roadmaps/hpc-engineer.md) — MPI/collectives background.
