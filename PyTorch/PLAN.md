# PyTorch — Plan

## Goal of this directory

Understand **PyTorch as a system**, not just as a library: how the repo is laid out, how it's built and tested, and how the compiler stack works. This is the foundation for contributing to PyTorch and for using it efficiently in my own work.

## What's already here

- [Explore](Explore.md) — repo layout (torch/, aten/, c10/), how to build and explore it, TORCH_LOGS.
- [CI_Infra](CI_Infra.md) — the CI/runner infrastructure (GitHub, AWS, LF, Google XPU, AMD) and actionlint.
- **Compilers/TorchInductor/EntryPoint** — how `torch.compile` reaches inductor (`compile` → `compile_fx`).
- **Core runtime internals**: [Tensor-TensorImpl-Storage](Tensor-TensorImpl-Storage.md), [Dispatch-Key](Dispatch-Key.md), [Autograd-Internals](Autograd-Internals.md), [Tensor-Subclass-Protocol](Tensor-Subclass-Protocol.md)
- **ATen / c10**: [ATen-Codegen](ATen-Codegen.md) (native_functions.yaml + codegen), [Custom-Ops](Custom-Ops.md) (torch.library)
- **Performance**: [Performance](Performance.md) (profiler, caching allocator, no_grad/inference_mode, DataLoader)
- Distributed (DDP/FSDP) → covered in [distributed-training](../MachineLearning/deeplearning/distributed-training.md)

## What to add next

- [x] **Core runtime internals** (the parts [Explore](Explore.md) points at but doesn't detail):
  - [x] `Tensor` → `TensorImpl` → `Storage`: views, strides, and memory formats (channels-last!)
  - [x] The **dispatch key** system and `__torch_dispatch__` (ties to [conditioning-on-a-python-subclass](../Programming/python/core/conditioning-on-a-python-subclass.md))
  - [x] Autograd internals: the `Function`/graph, `backward()`, custom autograd
  - [x] `torch.Tensor` vs `torch._TensorBase`, the subclass protocol
- [x] **ATen / c10 deep dive**:
  - [x] `native_functions.yaml` and the codegen (how ops are declared → generated)
  - [x] Writing a custom op (CPU + CUDA) — the practical contribution path
  - [x] `torch.library` (registering ops/fake impls — matters for torch.compile)
- [x] **Distributed**:
  - [x] DDP and FSDP — how training scales across GPUs → covered in [distributed-training](../MachineLearning/deeplearning/distributed-training.md)
  - [x] `torch.distributed` + NCCL/RCCL collectives (ties to [rocm-developers](../Roadmaps/rocm-developers.md)) → [Distributed-Collectives](Distributed-Collectives.md)
- [x] **Performance**:
  - [x] Profiling: `torch.profiler`, `torch.compile`, kernel timing
  - [x] Memory management: caching allocator, `torch.no_grad`/`inference_mode`
  - [x] Data loading: `DataLoader`/`Dataset` bottlenecks
- [ ] **Build & testing depth** (extends [CI_Infra](CI_Infra.md)):
  - [ ] Building from source walkthrough (CMake/ninja, dev containers)
  - [ ] Contributing checklist: tests, linters, CI etiquette (ties to [writing-a-good-commit](../OpenSource/writing-a-good-commit.md))

## Prioritization

1. **Tensor/Storage/dispatch** — the mental model everything else assumes.
2. **`torch.compile` internals** — my main interest (see [PLAN](../Compilers/PyTorchCompiler/PLAN.md)).
3. **Custom ops + autograd** — the practical "how do I extend PyTorch" knowledge.
