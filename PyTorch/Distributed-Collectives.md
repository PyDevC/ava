# torch.distributed and NCCL/RCCL

`torch.distributed` is PyTorch's distributed training layer; the *actual* communication runs on **NCCL** (NVIDIA) or **RCCL** (AMD ROCm) — both collective libraries derived from the MPI model (see [rocm-developers](../Roadmaps/rocm-developers.md)). The backend choice (`nccl`, `gloo`, `mpi`) decides what the collectives run on.

## Init and the process model

- **One process per GPU** (`mp.spawn` / `torchrun --nproc-per-node=N`). No threads sharing a device — the standard model.
- `init_process_group(backend="nccl", init_method=..., world_size=N, rank=r)` — sets up the group; `init_method` = TCP store / file / env (`MASTER_ADDR`/`MASTER_PORT`/`RANK`/`WORLD_SIZE`). `torchrun` manages these env vars for you.
- `dist.barrier()` / `dist.get_rank()` / `dist.get_world_size()` are the primitives for coordination.

## The collectives (the vocabulary)

- **`all_reduce(t, op=SUM)`** — sum/avg tensors across all ranks, all ranks get the result. DDP's gradient averaging is one `all_reduce` (see [distributed-training](../MachineLearning/deeplearning/distributed-training.md)).
- **`broadcast`** — one rank's tensor to all (parameter init sync, model loading).
- **`all_gather`** — concatenate each rank's tensor into a full set (FSDP's weight gathering, [distributed-training](../MachineLearning/deeplearning/distributed-training.md)).
- **`reduce_scatter`** — sum partial tensors then scatter the result (FSDP's gradient sharding).
- **`send`/`recv`** (point-to-point), `scatter`, `gather`, `reduce`.
- All are **blocking** (per default) and must be called in the *same order* on all ranks or you deadlock — the #1 distributed bug.

## NCCL/RCCL practicals

- **`nccl`** is the GPU backend: NVLink + InfiniBand aware, ring/tree algorithms, `TORCH_DISTRIBUTED_DETAIL=DEBUG` / `NCCL_DEBUG=INFO` for visibility.
- **`gloo`** — CPU/fallback (TCP-based), fine for small groups; **`mpi`** — legacy, MPI-installed only.
- **Async collectives**: `dist.all_reduce(..., async_op=True)` returns a handle — lets compute and comm overlap (the standard FSDP/DDP trick).
- **Batch size / seeds**: each rank gets a *different* data shard (`DistributedSampler`) and a *per-rank* seed offset — identical seed + identical shard = identical gradients = the sync is trivial but you've done no real parallelism.

## The failure modes

- **Deadlock**: mismatched collective order between ranks — log with `rank:` prefixes, always the fix.
- **Silent wrong results**: un-averaged batch norm (see [regularization-normalization](../MachineLearning/deeplearning/regularization-normalization.md) for sync-BN), rank-different data transforms.
- **Slow**: comm-bound (not overlapping), small message sizes (bucket padding matters in DDP).

## Related

- [distributed-training](../MachineLearning/deeplearning/distributed-training.md) — the training-pattern layer on top.
- [CI_Infra](CI_Infra.md) — where distributed tests run in CI.
- [rocm-developers](../Roadmaps/rocm-developers.md) — RCCL and the AMD side.
- [Performance](Performance.md) — profiler shows the comm gaps.
