# PyTorch — Plan

## Goal of this directory

Understand **PyTorch as a system**, not just as a library: how the repo is laid out, how it's built and tested, and how the compiler stack works. This is the foundation for contributing to PyTorch and for using it efficiently in my own work.

## What's already here

- [[Explore]] — repo layout (torch/, aten/, c10/), how to build and explore it, TORCH_LOGS.
- [[CI_Infra]] — the CI/runner infrastructure (GitHub, AWS, LF, Google XPU, AMD) and actionlint.
- **Compilers/TorchInductor/EntryPoint** — how `torch.compile` reaches inductor (`compile` → `compile_fx`).

## What to add next

- [ ] **Core runtime internals** (the parts [[Explore]] points at but doesn't detail):
  - [ ] `Tensor` → `TensorImpl` → `Storage`: views, strides, and memory formats (channels-last!)
  - [ ] The **dispatch key** system and `__torch_dispatch__` (ties to [[Programming/python/core/conditioning-on-a-python-subclass]])
  - [ ] Autograd internals: the `Function`/graph, `backward()`, custom autograd
  - [ ] `torch.Tensor` vs `torch._TensorBase`, the subclass protocol
- [ ] **ATen / c10 deep dive**:
  - [ ] `native_functions.yaml` and the codegen (how ops are declared → generated)
  - [ ] Writing a custom op (CPU + CUDA) — the practical contribution path
  - [ ] `torch.library` (registering ops/fake impls — matters for torch.compile)
- [ ] **Distributed**:
  - [ ] DDP and FSDP — how training scales across GPUs
  - [ ] `torch.distributed` + NCCL/RCCL collectives (ties to [[Roadmaps/rocm-developers]])
- [ ] **Performance**:
  - [ ] Profiling: `torch.profiler`, `torch.compile`, kernel timing
  - [ ] Memory management: caching allocator, `torch.no_grad`/`inference_mode`
  - [ ] Data loading: `DataLoader`/`Dataset` bottlenecks
- [ ] **Build & testing depth** (extends [[CI_Infra]]):
  - [ ] Building from source walkthrough (CMake/ninja, dev containers)
  - [ ] Contributing checklist: tests, linters, CI etiquette (ties to [[OpenSource/writing-a-good-commit]])

## Prioritization

1. **Tensor/Storage/dispatch** — the mental model everything else assumes.
2. **`torch.compile` internals** — my main interest (see [[Compilers/PyTorchCompiler/PLAN]]).
3. **Custom ops + autograd** — the practical "how do I extend PyTorch" knowledge.
