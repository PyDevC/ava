# Building PyTorch from source

Building PyTorch is a rite of passage for contributors — it takes an hour+, and getting it *right* the first time saves an afternoon. The flow is standard CMake + ninja + a Python env, but the scale and the submodule setup have their own gotchas. (For what CI does with the build, see [CI_Infra](CI_Infra.md); for the repo layout, see [Explore](Explore.md).)

## Setup: clone, submodules, env

1. **Clone with submodules**: `git clone --recursive https://github.com/pytorch/pytorch.git` — third-party deps (FBGEMM, XNNPACK, ONNX, eager-mode extensions) come in via submodules. Missed them? `git submodule update --init --recursive`.
2. **Conda env** (PyTorch's documented path) with the pinned compilers: `conda create -n pytorch-build cmake ninja python=3.12` plus `mkl`/`mkl-include` for CPU MKL builds. Use the versions the README pins — the PyTorch build matrix is sensitive to compiler/CMake versions.
3. **Install deps**: `pip install -r requirements.txt` (the dev tools: pytest, pyyaml, etc. — see [Contributing](Contributing.md)).

## The build command

- **The one-liner**: `python setup.py develop` (older docs) or the modern `pip install -e .` — both do an editable install: the build lands in `torch/` in-tree, and imports pick it up directly.
- **Editable build ≠ make**: after the first build, iterate with ninja directly: `ninja -C build` rebuilds just the changed targets — *much* faster than rerunning setup.py (see the ninja section below).
- First build time: 30 min (fast machine, all cores) to 2+ hours. This is normal, not a hang — watch for a progressing build dir.

## CMake options that matter

```
USE_CUDA=1            # NVIDIA backend (needs CUDA toolkit + cuDNN)
USE_ROCM=1            # AMD backend (see [rocm-developers](../Roadmaps/rocm-developers.md))
USE_MKL=1             # MKL-backed CPU ops (great for CPU benchmark work)
CMAKE_BUILD_TYPE=Release / RelWithDebInfo   # RelWithDebInfo = debuggable
MAX_JOBS=16           # limit ninja parallelism (OOM guard on CI machines)
DEBUG=1               # very slow, only for deep C++ debugging
```

- Set them as env vars before `python setup.py develop`, or pass to `cmake -D` if you're driving CMake directly.
- `USE_CUDA` off by default → a CPU-only build is the fastest first build; add backends after the basic flow works.

## Ninja — why the rebuild is fast

- PyTorch generates a ninja build in `build/`. Ninja's claim to fame: it tracks the full dependency graph, so a change to `aten/src/ATen/native/...` rebuilds only that translation unit and relinks, not the world.
- Rule: after the first `setup.py develop`, live in `build` + `ninja -C build`. The one thing that *does* require re-running setup.py: adding a file to the build or changing `native_functions.yaml` (the codegen, see [ATen-Codegen](ATen-Codegen.md)).

## Dev containers

- Official dev containers (`dev/containers/`) give a pre-baked image (CUDA/ROCm variants) so you skip compiler/driver roulette. Worth using when the host toolchain is unfamiliar — they're how CI-like environments get reproduced locally (see [CI_Infra](CI_Infra.md) for the runner side).

## Debugging the build

- **Link errors** — the classic: version mismatch between the pinned CMake/compiler and what you installed. `python setup.py develop` prints the config; mismatch → redo the env.
- **OOM during build** — `MAX_JOBS=4` or the `CMAKE_BUILD_TYPE` swap; ninja parallelism on 64 cores can eat 60+ GB.
- **Symbol/import errors at runtime** — usually a stale build; `rm -rf build` + full rebuild. For actual debugger/sanitizer work, see the build-from-source section of [cpp-debugging](../Programming/cpp/cpp-debugging.md).

## Why it matters

Everything after this — running a custom op, hacking an inductor pass, fixing a dispatch bug — needs a working local build (see [Contributing](Contributing.md) and the PR tasks in [pytorch-developers](../Roadmaps/pytorch-developers.md)). The build is the bootstrap cost of all contribution work; getting it muscle-memory saves hours every time.

## Related

- [Explore](Explore.md) — what the repo contains and how to walk it.
- [CI_Infra](CI_Infra.md) — how CI builds/tests the same tree at scale.
- [Contributing](Contributing.md) — the checklist that starts with a working build.
- [ATen-Codegen](ATen-Codegen.md) — what `native_functions.yaml` changes do to the build.
