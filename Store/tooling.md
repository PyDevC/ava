# Tools worth installing

The curated toolchain — links only, grouped by purpose. When a task needs profiling/building/tuning, this is where I look first.

## Profilers

- NVIDIA Nsight — https://developer.nvidia.com/nsight-systems
- `perf` — Linux CPU profiler: https://perf.wiki.kernel.org
- ROCm profiler (rocprof) — https://rocm.docs.amd.com/projects/rocprofiler/en/latest/
- py-spy — Python sampling profiler: https://github.com/benfred/py-spy
- cProfile — stdlib: https://docs.python.org/3/library/profile.html

## Compilers / toolchains

- Clang — https://clang.llvm.org
- GCC — https://gcc.gnu.org
- LLVM tools (opt, llvm-mca, llvm-nm...) — https://llvm.org/docs/CommandGuide/
- MLIR / mlir-opt — https://mlir.llvm.org
- CMake — https://cmake.org
- Ninja — https://ninja-build.org
- ccache (build caching) — https://ccache.dev

## Autotuners / optimization

- Optuna — https://optuna.org (see [optuna](../MachineLearning/automl/hyperparameter-tuning/optuna.md))
- Apache TVM AutoTVM/Ansor — https://tvm.apache.org

## Python / dev tooling

- uv — Python env/package manager: https://docs.astral.sh/uv/
- ruff (linter + formatter) — https://docs.astral.sh/ruff/
- pre-commit — https://pre-commit.com
- mypy — https://mypy-lang.org
- pyright — https://github.com/microsoft/pyright
- pytest — https://docs.pytest.org
- ripgrep (rg) — https://github.com/BurntSushi/ripgrep
- jq — https://jqlang.github.io/jq/

## Why it matters

Most of these are one-time installs that pay for themselves the first week: profilers answer "where does the time go", compilers/toolchains are the daily driver of compiler work, and the Python set is the standard stack of every repo I touch (see [python-tooling](../Programming/python/python-tooling.md)).

## Related

- [PLAN](./) — the Store area this lives in.
- [glossary](glossary.md) — the acronyms (ASAN, TSAN...) these tools appear under.
- [roofline-model](roofline-model.md) — the mental model profilers confirm.
