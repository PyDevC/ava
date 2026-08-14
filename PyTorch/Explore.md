# Explore the repo

A knowledge base page on how to navigate the PyTorch monorepo, what lives where, and what is worth checking out first.

## Top level layout

- `torch/` - the Python package, this is where most of the work happens
    - `torch/_dynamo/` - the tracing frontend, converts eager Python into FX graphs
    - `torch/_inductor/` - the default compiler backend (see [EntryPoint](../Compilers/PyTorchCompiler/TorchInductor/EntryPoint.md))
    - `torch/_functorch/` - function transforms (grad, vmap, etc)
    - `torch/onnx/` - ONNX export
    - `torch/distributed/` - distributed training, `dist.all_reduce` and friends
- `aten/` - the C++ tensor library (A Ten sor), plus the `native_functions.yaml` registry that code-gen picks up
    - `aten/src/ATen/native/` - operator implementations per backend (cpu, cuda, mps, cudnn, mkl)
    - `aten/src/ATen/core/` - core abstractions like `TensorImpl`, `DispatchKey`
    - `aten/src/THC/` - legacy CUDA support headers (mostly removed/deprecated now)
- `c10/` - low level utilities: intrusive pointer, dispatcher, device types
- `torch/csrc/` - C++ glue that binds the Python API to ATen, includes the autograd engine
- `test/` - the tests. Python tests in `test/`, C++ tests in `test/cpp`
- `.ci/` and `.github/workflows/` - the CI config (see [CI-Infra](CI-Infra.md))
- `scripts/`, `tools/`, `third_party/` - build helpers, codegen scripts, vendored deps

## Things to check when exploring

- Is torchvision migrating to use ATen Headers? torchvision used to include `THC/THCDeviceUtils.cuh` and friends; over time PyTorch has been deprecating the `THC` legacy headers in favour of `ATen/cuda/...` ones. So before writing new CUDA code in torchvision, check whether it still relies on the deprecated `THC` headers or has already moved over to `ATen`. As of the torchvision 0.7.0 era PRs were already removing `CUDAApplyUtils.cuh` includes and moving to the ATen equivalents. Always verify against the current tree, since this migration is ongoing.

## How to get familiar

- Start by using PyTorch, then look up where the op you used lives. E.g. call `torch.matmul`, then grep for `matmul` in `native_functions.yaml` and follow the dispatch entry.
- Use `git log`/`git blame` on the file you care about to see recent changes and the maintainers touching it.
- Read the contribution guide (`CONTRIBUTING.md`) and the wiki (see the "How to explore large repos" page in OpenSource/).
- Use `TORCH_LOGS=+dynamo,+inductor` (or `TORCH_LOGS=+help`) to see exactly what the compiler is doing while you trace.
