# PyTorch Developers

PyTorch developers contribute to PyTorch itself (or build heavily on its internals): core C++ (`c10`, ATen), Python frontend, autograd, `torch.compile` (Dynamo + Inductor), distributed, or ops/backends.

## The stack to know (bottom-up)

- **c10** — core types: `TensorImpl`, `DispatchKey`, `IValue`/`Object` (the serialization/IR value types).
- **ATen** — the operator library: `Tensor` API, CPU/CUDA kernels, native functions, codegen from `native_functions.yaml`.
- **Python binding layer** — `torch/csrc` (pybind/THP), how `torch.add` reaches ATen.
- **Autograd** — the graph that backprop builds; custom backward functions.
- **torch.compile** — Dynamo (bytecode tracing → FX graph) + AOTAutograd + Inductor (codegen). See [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md), [GraphBreaks](../Compilers/PyTorchCompiler/TorchDynamo/GraphBreaks.md), and [EntryPoint](../Compilers/PyTorchCompiler/TorchInductor/EntryPoint.md).
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

## PR-sized tasks

The roadmap mapped to actual contribution-sized pieces — each is a real, mergeable PR (see [Contributing](../PyTorch/Contributing.md) for the full checklist):

1. **Write a custom op** (CPU + CUDA, via `torch.library`) — the classic first contribution: full dispatch, tests, and codegen exposure in one small package (see [Custom-Ops](../PyTorch/Custom-Ops.md)).
2. **Open a PR adding a test** — pick an existing op, find a missing edge case, add the regression test to the right `test/` file. Tiny in code, huge in learning the test matrix + CI etiquette.
3. **Fix a dispatch issue** — a bug report about wrong backend selection / missing key on a subclass: add the missing dispatch-key handling (see [Dispatch-Key](../PyTorch/Dispatch-Key.md), [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md)).
4. **Work on an inductor pass** — a fusion or layout decision in TorchInductor (see [PLAN](../Compilers/PyTorchCompiler/), [Inductor-IR](../Compilers/PyTorchCompiler/TorchInductor/Inductor-IR.md)): benchmark-driven, needs a local build to iterate ([Build-From-Source](../PyTorch/Build-From-Source.md)).
5. **Perf fix** — profile a hot op, identify a kernel or allocator problem, fix it with a benchmark attached (see [Performance](../PyTorch/Performance.md)).

Order matters: custom op → test PR → dispatch fix → inductor pass. Each requires the one before it, and step 4 is where the "compiler engineer" resume line comes from.

## Related

- [ai-framework-engineer](ai-framework-engineer.md) — the wider role this is a specialization of.
- [triton-compiler-engineer](triton-compiler-engineer.md) — Inductor's GPU codegen is Triton.
- [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md) — the compiler half.
