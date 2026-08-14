# Tensor subclass protocol

`torch.Tensor` is a C++ class with a **Python-facing subclass protocol** — the officially supported way to build tensors with custom behavior. `torch.Tensor` vs `torch._TensorBase` is the split: `_TensorBase` is the (mostly C++) core; `Tensor` is the Python facade you subclass.

## The protocol surface

- **`Tensor` is the public type**: you subclass it (`class MyTensor(torch.Tensor)`). `_TensorBase` exists so subclassing doesn't drag in everything; most methods live on the base and the subclass overrides hooks.
- **The hooks**:
  - `__torch_function__` — intercept *any* torch API call on instances (the broad hook; legacy-ish).
  - `__torch_dispatch__` — intercept *op-level* dispatch (modern; the one the framework uses, see [Dispatch-Key](Dispatch-Key.md)).
  - `__new__` (not `__init__`) — tensor constructors; subclass `__new__(cls, *args, ...)` returns a subclassed tensor.
  - `_make_wrapper_subclass` — the factory for wrapper subclasses: gives you a real storage + dispatch hooks without reimplementing every method.
- `requires_grad`, device/dtype/`is_contiguous` flow through the underlying `TensorImpl` (see [Tensor-TensorImpl-Storage](Tensor-TensorImpl-Storage.md)).

## The two things people build with it

1. **Wrapper subclasses** (the canonical use): FakeTensor (compile-time shape probing, [FX-Graph-IR](../Compilers/PyTorchCompiler/FX-Graph-IR.md)), FunctionalTensor (mutations → functional, [AOTAutograd](../Compilers/PyTorchCompiler/AOTAutograd.md)), distributed sharded tensors (DTensor). These wrap an *inner* real tensor and redefine dispatch.
2. **Behavioral subclasses**: logging, quantized-like semantics, tracking — hook ops, log, delegate.

## Why `__torch_dispatch__` won

The framework chose `__torch_dispatch__` because it's *op-precise*: you see each `torch.add(...)` as a call and can return any tensor (even a non-subclass) — giving FakeTensor/FunctionalTensor their power. `__torch_function__` intercepts at the *API* level (messy, everything funnels through). The compile stack *relies* on dispatch subclasses — see [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md) for the `isinstance`/`__instancecheck__` twist that makes them work with compiled code.

## The gotchas

- Subclasses break equality/`isinstance` assumptions in third-party code; `torch._C._TensorBase` vs `Tensor` confusion is common.
- Dispatch-based subclasses must handle the `Meta`/`FakeTensor` world too (their `__torch_dispatch__` sees the fake calls).
- Overriding `__new__` wrong = subtle construction bugs; `_make_wrapper_subclass` avoids most of them.

## Related

- [Dispatch-Key](Dispatch-Key.md) — the mechanism `__torch_dispatch__` plugs into.
- [Tensor-TensorImpl-Storage](Tensor-TensorImpl-Storage.md) — the layers a subclass sits on.
- [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md) — the compile-interaction subtlety.
- [Custom-Ops](Custom-Ops.md) — the alternative extension point (ops vs subclasses).
