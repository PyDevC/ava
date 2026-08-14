# Dispatch keys

The **dispatch key** is the mechanism that lets one op name (`torch.add`) reach the right implementation — CPU, CUDA, autograd, `torch.compile`, custom extensions. PyTorch's op resolution is a *stacked* system: each key is a layer that may handle the call or hand it down.

## The layering (top to bottom, simplified)

```
PythonKey      — __torch_dispatch__ hooks, subclasses
Autograd       — builds the autograd graph (see [[Autograd-Internals]])
CompositeExplicitAutograd
Functionalize  — makes mutations functional (see [[Compilers/PyTorchCompiler/AOTAutograd]])
TorchFunction  — __torch_function__ (subclass dispatch)
PythonTLSSnapshot
Mode/Compound keys — torch.compile / FakeTensor / FunctionalTensor modes
Backend keys    — CPU, CUDA, MPS, XPU, Meta, Lazy, MKLDNN...
```

Each op call walks down the stack until a layer claims it. `set_default_dispatch_key` / `torch._C._get_dispatch_key` let you inspect. This is why `torch.cuda.synchronize()`-style hacks exist: layers insert behavior between the Python call and the CUDA kernel.

## The two override hooks

- **`__torch_dispatch__`** — called *before* autograd/backend resolution for ops on your tensor subclass. The power lever: your class intercepts `torch.add(...)` etc. and can redirect (this is how FakeTensor, FunctionalTensor, `torch.compile`'s tracing, and distributed checkpointing all hook in). See [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md) for why subclass dispatch is the canonical extension point.
- **`__torch_function__`** — the legacy whole-API hook (lower-level, called for *any* torch function on your type).

## The dispatch-key practicals

- **FakeTensor / Meta device**: a `Meta`-keyed tensor that never allocates — used by `torch.compile` to infer shapes without memory (see [FX-Graph-IR](../Compilers/PyTorchCompiler/FX-Graph-IR.md)).
- **Autograd is a dispatch key too** — `requires_grad` sets the Autograd key, which builds the backward graph. `torch.no_grad()` *disables* that layer (cheaper, no graph).
- **Custom ops** (see [Custom-Ops](Custom-Ops.md)): your op gets a kernel per backend key; dispatch picks it. `torch.library` lets you register `FakeTensor`/`Meta` impls so `torch.compile` can trace your op without running it.

## Related

- [Tensor-TensorImpl-Storage](Tensor-TensorImpl-Storage.md) — what the keys read from the tensor.
- [Autograd-Internals](Autograd-Internals.md) — the Autograd key in detail.
- [Custom-Ops](Custom-Ops.md) — registering per-key kernels.
- [AOTAutograd](../Compilers/PyTorchCompiler/AOTAutograd.md) — functionalization lives in this stack.
