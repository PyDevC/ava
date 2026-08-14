# ATen and native_functions.yaml codegen

ATen is PyTorch's **C++ tensor library** — the `aten::` ops that back everything. What makes it scale is that ops are *declared once* in `native_functions.yaml` and **codegen** generates the declarations, dispatcher entries, Python bindings, and accessors. Writing a core op = editing a YAML file + writing one kernel.

## native_functions.yaml

```yaml
- func: add.Tensor(Tensor self, Tensor other, *, Scalar alpha=1) -> Tensor
  variants: [function, method]
  device_check: NoCheck
  dispatch:
    CPU: add_kernel_cpu
    CUDA: add_kernel_cuda
    CompositeExplicitAutograd: add_out_cpu_meta
```

Each entry: the **op schema** (`func:`), whether it's also a method, its `dispatch` table (which kernel per dispatch key, see [Dispatch-Key](Dispatch-Key.md)), `variants`, `structured:`, tag lists (e.g. `tags: core`). The schema IS the source of truth — C++ signatures, Python signatures, and the dispatcher all derive from it.

## What codegen produces

- `Declarations.yaml` / `Functions.h` — C++ `at::add(...)` decls.
- The **dispatcher registry** (`RegisterDispatchKey.cpp`) — wires schema → kernels per key.
- Python bindings (`torch/.../python_bindings.cpp`) — so `torch.add` and `tensor.add` exist.
- `torch.ops` accessors, `torch._C` symbols, and the `CompositeImplicit`/`CompositeExplicitAutograd` registrations.

That's why "add an op to PyTorch" is: write yaml → write a native kernel (in `aten/src/ATen/native/`) → rebuild → `torch.add` exists. `tools/codegen/gen.py` is the orchestrator (see [Explore](Explore.md) for the repo map).

## The two op worlds

- **CompositeExplicitAutograd / CompositeImplicit**: implemented as compositions of other ops (e.g. `sum(dim=1)` = `sum(dim=1, keepdim=False)` decomposition) — get autograd for free by falling through to primitives.
- **Native (per-backend)**: real kernels with `dispatch:` entries (CPU/CUDA/Meta/MKL...). Autograd for these comes from `autograd/FunctionsManual.cpp` or custom `Function` definitions.

## The `torch.library` connection

`torch.library.define` (see [Custom-Ops](Custom-Ops.md)) is the *extension* API that mirrors this yaml pipeline: schema + per-key impls, registered at runtime instead of codegen. Native ops = yaml + codegen; custom ops = `torch.library` + decorators. Same dispatcher underneath.

## Practical debugging

- `torch._C._dispatch_dump()` / `TORCH_DISPATCH_DEBUG` — see which kernel a call resolves to.
- Wrong signature in yaml → codegen fails with a clear schema error (the schema parser is strict).
- A missing `Meta`/`CompositeExplicitAutograd` entry = `torch.compile` can't trace the op (see [GraphBreaks](../Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks.md)).

## Related

- [Dispatch-Key](Dispatch-Key.md) — what the dispatch tables key on.
- [Custom-Ops](Custom-Ops.md) — the runtime equivalent of this machinery.
- [Explore](Explore.md) — where the yaml and codegen live in the repo.
- [FX-Graph-IR](../Compilers/PyTorchCompiler/FX-Graph-IR.md) — the graph ops compile from.
