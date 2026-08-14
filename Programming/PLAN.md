# Programming — Plan

## Goal of this directory

Organize **programming-language knowledge**, one sub-directory per language/area. The goal isn't "learn X" — it's the *internals and sharp edges* that matter for systems work: execution models (bytecode, type systems), memory, and the language features that trip people up.

## What's already here

- **python/core/** — Python internals: [bytecode](python/core/bytecode.md), [typechecking](python/core/typechecking.md), [conditioning-on-a-python-subclass](python/core/conditioning-on-a-python-subclass.md), plus the execution model, data model, C API, and performance notes (see its own [PLAN](python/core/PLAN.md)).
- **python/** — [packaging](python/packaging.md), [asyncio](python/asyncio.md) (see [PLAN](python/PLAN.md)).
- **cpp/** — C++ memory model, smart pointers, templates/STL, debugging, CMake (see [PLAN](cpp/PLAN.md)).

## What to add next

- [x] **C++** (needed for all the compiler/GPU work — see [ai-framework-engineer](../Roadmaps/ai-framework-engineer.md)):
  - [x] A `cpp/` sub-directory: move semantics, RAII, templates/CRTP, STL performance, memory layout
  - [x] Build systems: CMake basics (extends [CMake_Guide](../Compilers/MLIR/CMake_Guide.md))
  - [x] Debugging: gdb/lldb, sanitizers (ASAN/TSAN), profilers (perf)
- [x] **Python non-core topics**:
  - [x] The C API / CPython extension writing (ties to [bytecode](python/core/bytecode.md) and PyTorch bindings)
  - [x] GIL and threading vs multiprocessing (ties to PyTorch DataLoader perf)
  - [x] Memory model: refcounting, GC, `weakref` (a natural companion note)
- [ ] **Bash/shell** (day-to-day tooling) — a small `shell/` section.
- [ ] **Version control beyond basics** — interactive rebase, bisect, worktrees, submodules (useful for OSS work; see [OpenSource](../OpenSource/PLAN.md)).

## Prioritization

1. **C++ + CMake + debugging** — unblocks compiler/GPU contribution work.
2. **Python C API + GIL** — the PyTorch-specific knowledge.
3. **git advanced** — small but immediately useful.
