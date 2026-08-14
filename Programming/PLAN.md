# Programming — Plan

## Goal of this directory

Organize **programming-language knowledge**, one sub-directory per language/area. The goal isn't "learn X" — it's the *internals and sharp edges* that matter for systems work: execution models (bytecode, type systems), memory, and the language features that trip people up.

## What's already here

- **python/core/** — Python internals: [[core/bytecode]], [[core/typechecking]], [[core/conditioning-on-a-python-subclass]], plus the execution model, data model, C API, and performance notes (see its own [[core/PLAN]]).
- **python/** — [[python/packaging]], [[python/asyncio]] (see [[python/PLAN]]).
- **cpp/** — C++ memory model, smart pointers, templates/STL, debugging, CMake (see [[cpp/PLAN]]).

## What to add next

- [x] **C++** (needed for all the compiler/GPU work — see [[../Roadmaps/ai-framework-engineer]]):
  - [x] A `cpp/` sub-directory: move semantics, RAII, templates/CRTP, STL performance, memory layout
  - [x] Build systems: CMake basics (extends [[../Compilers/MLIR/CMake_Guide]])
  - [x] Debugging: gdb/lldb, sanitizers (ASAN/TSAN), profilers (perf)
- [x] **Python non-core topics**:
  - [x] The C API / CPython extension writing (ties to [[core/bytecode]] and PyTorch bindings)
  - [x] GIL and threading vs multiprocessing (ties to PyTorch DataLoader perf)
  - [x] Memory model: refcounting, GC, `weakref` (a natural companion note)
- [ ] **Bash/shell** (day-to-day tooling) — a small `shell/` section.
- [ ] **Version control beyond basics** — interactive rebase, bisect, worktrees, submodules (useful for OSS work; see [[../OpenSource]]).

## Prioritization

1. **C++ + CMake + debugging** — unblocks compiler/GPU contribution work.
2. **Python C API + GIL** — the PyTorch-specific knowledge.
3. **git advanced** — small but immediately useful.
