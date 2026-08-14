# Tensor, TensorImpl, Storage

`torch.Tensor` is a **view over memory** — it does not own its data directly. The split is `Tensor` (Python-facing handle) → `TensorImpl` (the C++ core: shape, dtype, layout) → `Storage` (the actual buffer). Understanding the split explains views, strides, memory formats, and a whole class of "why is my tensor slow/wrong" bugs.

## The three layers

```
Tensor (Python / C++ handle, the "smart pointer")
  └─ TensorImpl — metadata only: sizes[], strides[], dtype, device, layout, requires_grad
       └─ Storage — the raw allocation: data_ptr, size (bytes/elements), allocator, refcount
```

- **`TensorImpl`** is *just metadata*. Two tensors can share one `TensorImpl` (e.g. `view`-shaped aliases).
- **`Storage`** owns (or references) the bytes. A tensor with zero elements, an offset, and a `view` all still reference one `Storage`.
- `Tensor` adds the Python-facing API (`tensor.add(...)`, `tensor.data`, gradients).

## Views, strides, memory formats

- **View**: a new `TensorImpl` over the same `Storage` with different `sizes`/`strides` — `t[1:3]`, `t.T`, `t.unsqueeze(0)` are views (zero-copy). Copy-creating ops (`t.clone()`, `t.contiguous()`, `t.to(...)`) materialize a new `Storage`.
- **Strides**: `(row_stride, col_stride)` per dimension — what makes `[M,N]` "row-major contiguous" vs **channels-last** (`[N,C,H,W]` with stride `[1, C*H*W, C*W, C]`). Memory format is just *a stride pattern* (see `torch.channels_last`).
- **The classic bug**: a non-contiguous tensor silently makes every kernel copy. `TORCH_LOGS=layout` / `tensor.stride()` / `tensor.is_contiguous()` are the diagnosis. The optimizer-fusion angle: contiguous + channels-last affects *which* kernels can fuse (see [[Compilers/PyTorchCompiler/TorchInductor/Fusion-Scheduler]]).

## The implications

- **Aliasing is real**: two Python tensors can share memory. In-place ops on a view affect the base. This is why functionalization matters for the compiled world (see [[Compilers/PyTorchCompiler/AOTAutograd]]).
- **`TensorImpl` is what the dispatcher keys on** (dtype, device, layout → dispatch key, see [[Dispatch-Key]]).
- Refcounting lives at the `Storage` level (memory is freed when no `TensorImpl` references it) — the "caching allocator" story (see [[../PyTorch]] perf notes).

## Related

- [[Dispatch-Key]] — dispatch reads the TensorImpl metadata.
- [[Custom-Ops]] — a custom op's CPU/CUDA impls operate on `TensorImpl`/`Storage`.
- [[Compilers/PyTorchCompiler/FX-Graph-IR]] — the graph world where aliasing is functionalized away.
