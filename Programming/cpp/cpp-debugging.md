# C++ debugging: gdb, sanitizers, perf

The debugging stack for the compiler/GPU work — and for reading C++ crashes in PyTorch/MLIR/LLVM. Order of operations: **reproduce → minimize → sanitizer → debugger → profile**.

## Sanitizers first (they find it for you)

- **ASAN** (AddressSanitizer): use-after-free, buffer overflow, stack-overflow, double-free — the #1 class of C++ bugs. `-fsanitize=address` + `-fno-omit-frame-pointer`. Slower, always on in dev.
- **UBSAN** (undefined behavior): `-fsanitize=undefined` — signed overflow, bad shifts, null-deref. Cheap, run always.
- **TSAN** (thread): data races — `-fsanitize=thread`. Only with real threads (see [python-c-api](../python/core/python-c-api.md) for the GIL-released world).
- **LSAN** (leak): comes with ASAN (`detect_leaks=1`) — finds the leaks from [cpp-memory-model](cpp-memory-model.md) mistakes.
- Pattern: run your test under ASAN → it prints the *exact* allocation + faulting access + stack trace → you fix one line. The "magic" of C++ debugging is mostly this.

## gdb/lldb

- The debugger: `gdb ./binary`, `break`, `run`, `bt` (backtrace), `p var` (print), `continue`, `finish`. The `bt` after a crash is the reflex.
- **Core dumps**: `ulimit -c unlimited`, `gdb ./binary core` — debugging a crash post-mortem.
- Python/C++ interop: `gdb python` with the `python-gdb.py` extension → `py-bt` shows the *Python* frames under a C++ crash (invaluable for PyTorch extension crashes, see [python-c-api](../python/core/python-c-api.md)).
- LLVM/MLIR have `LLVM_ENABLE_ABI_BREAKING_CHECKS`/assert builds — asserts are your cheapest debugger (see [CMake-Guide](../../Compilers/MLIR/CMake-Guide.md)).

## perf (the profiler)

- `perf record ./binary` → `perf report` — function-level CPU profile. The `perf top` live view.
- The golden rule: **profile before optimizing** (see [python-performance](../python/core/python-performance.md) for the same rule in Python). C++ is not "fast by default" — the hot 1% function decides everything.
- `perf stat` for counters (cache misses, branch mispredicts); `gprof` for the quick-and-dirty call graph.

## The workflow that works

1. **Reproduce** smallest-possible input (minimize — see [debugging-CI-failures](../../OpenSource/debugging-CI-failures.md) for the CI version).
2. **ASAN/UBSAN** build → run → read the first error.
3. **gdb** on the stack trace → understand *why* (ownership bug, off-by-one, dangling `const&`).
4. **perf** if it's "slow" not "wrong".

## Related

- [cpp-memory-model](cpp-memory-model.md) / [cpp-smart-pointers](cpp-smart-pointers.md) — the bug classes sanitizers detect.
- [debugging-CI-failures](../../OpenSource/debugging-CI-failures.md) — CI logs are the same skills at scale.
- [python-c-api](../python/core/python-c-api.md) — `py-bt` for extension crashes.
- [CMake-Guide](../../Compilers/MLIR/CMake-Guide.md) — build flags for assert/sanitizer builds.
