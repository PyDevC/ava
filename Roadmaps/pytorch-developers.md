# PyTorch Developers

PyTorch developers contribute to PyTorch itself (or build heavily on its internals): core C++ (`c10`, ATen), Python frontend, autograd, `torch.compile` (Dynamo + Inductor), distributed, or ops/backends.

## The stack to know (bottom-up)

- **c10** — core types: `TensorImpl`, `DispatchKey`, `IValue`/`Object` (the serialization/IR value types).
- **ATen** — the operator library: `Tensor` API, CPU/CUDA kernels, native functions, codegen from `native_functions.yaml`.
- **Python binding layer** — `torch/csrc` (pybind/THP), how `torch.add` reaches ATen.
- **Autograd** — the graph that backprop builds; custom backward functions.
- **torch.compile** — Dynamo (bytecode tracing → FX graph) + AOTAutograd + Inductor (codegen). See [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md), [GraphBreaks](../Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks.md), and [EntryPoint](../PyTorch/Compilers/TorchInductor/EntryPoint.md).
- **Distributed** — DDP, FSDP, NCCL/RCCL, `torch.distributed`.

## How to contribute

1. Get the build working (CMake + ninja; takes a while — see the build guide in [Explore](../PyTorch/Explore.md)).
2. Read the contributor guide; pick a **good first issue** (often op implementations, tests, docs).
3. Understand codegen: many ops are declared in YAML and generated — don't hand-write boilerplate.
4. CI is everything: tests + linters (see [CI_Infra](../PyTorch/CI_Infra.md)).

## Typical tasks

- Adding a new op or optimizing an existing kernel.
- Fixing a dispatch/subclass bug (see [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md)).
- Improving a compiler pass (inductor fusion, triton codegen).
- Benchmarking and perf fixes on CPU/CUDA.

## Roadmap

1. C++ + Python fluency; use PyTorch deeply as a user.
2. Reimplement small pieces (a custom autograd Function, a custom op) to learn the seams.
3. Build from source; fix one thing; iterate.

## Related

- [ai-framework-engineer](ai-framework-engineer.md) — the wider role this is a specialization of.
- [triton-compiler-engineer](triton-compiler-engineer.md) — Inductor's GPU codegen is Triton.
- [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md) — the compiler half.
