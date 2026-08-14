# Custom ops and torch.library

The practical "extend PyTorch" path: write an op in C++ (CPU/CUDA), register it, and make it work with `torch.compile`. This is also the *contribution* path into PyTorch itself (native ops go through the same machinery via `native_functions.yaml`, see [[../PyTorch/PLAN]]).

## The modern API: `torch.library`

```python
torch.library.define("mylib::quantize", "(Tensor x, int bits) -> Tensor")
@torch.library.impl("mylib::quantize", "CPU")
def quantize_cpu(x, bits): ...
@torch.library.impl("mylib::quantize", "CUDA")
def quantize_cuda(x, bits): ...
```

- **`torch.library.define`** — declare the op signature (name, schema: arg types → result types). The schema is the contract the dispatcher ([[Dispatch-Key]]) uses.
- **`torch.library.impl`** — attach a kernel per dispatch key (`CPU`, `CUDA`, `Meta`, `CompositeExplicitAutograd`, ...).
- C++ ops live in `torch/csrc/ops` style; Python ops via `torch.library` can wrap C++ kernels (`torch.ops.mylib.quantize.default`).

## What compile needs from your op

For `torch.compile` to handle a custom op (instead of graph-breaking), it must be able to **trace it without running it**. Register:

1. **`Meta` / `FakeTensor` impl** — computes the output *shapes/dtypes* from input shapes without allocating (this is what makes shape inference work, see [[Compilers/PyTorchCompiler/FX-Graph-IR]]). Missing this = `torch.compile` fails or breaks.
2. **`CompositeExplicitAutograd`** or a **`torch.autograd.Function`** — so backward can be traced (see [[Autograd-Internals]]).
3. Optionally register with `torch.compiler.allow_in_graph` to force in-graph placement.

If your op can't satisfy these, `torch.compile` emits a graph break (see [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]]).

## The `native_functions.yaml` route (contributing upstream)

Core ops are declared in `aten/src/ATen/native/native_functions.yaml`, and **codegen** produces the C++ decls, the Python bindings, the dispatcher entries, and the `torch.ops` accessors. A contribution = yaml entry + `native/` kernel + tests. The `torch.library` path is the *extension* equivalent of the same pipeline (see [[PyTorch/PLAN]] for the repo-layout map in [[Explore]]).

## The practical checklist

- Name your op `namespace::op` (namespaced, lowercase, no collisions).
- Provide the schema exactly (`Tensor`/`int`/`str` types, tensor-arg ordering).
- Register Meta + CPU first; CUDA if you have a kernel; then test `torch.compile(fullgraph=True)` on a model using it.

## Related

- [[Dispatch-Key]] — what `impl(..., key)` registers against.
- [[Autograd-Internals]] — custom `torch.autograd.Function` integration.
- [[Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks]] — what happens without the Meta impl.
- [[Programming/python/core/conditioning-on-a-python-subclass]] — the other extension point.
