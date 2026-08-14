# Programming — Plan

## Goal of this directory

Organize **programming-language knowledge**, one sub-directory per language/area. The goal isn't "learn X" — it's the *internals and sharp edges* that matter for systems work: execution models (bytecode, type systems), memory, and the language features that trip people up.

## What's already here

- **python/core/** — Python internals: [[core/bytecode]], [[core/typechecking]], [[core/conditioning-on-a-python-subclass]] (see its own [[core/PLAN]]).

## What to add next

- [ ] **C++** (needed for all the compiler/GPU work — see [[../Roadmaps/ai-framework-engineer]]):
  - [ ] A `cpp/` sub-directory: move semantics, RAII, templates/CRTP, STL performance, memory layout
  - [ ] Build systems: CMake basics (extends [[../Compilers/MLIR/CMake_Guide]])
  - [ ] Debugging: gdb/lldb, sanitizers (ASAN/TSAN), profilers (perf)
- [ ] **Python non-core topics**:
  - [ ] The C API / CPython extension writing (ties to [[core/bytecode]] and PyTorch bindings)
  - [ ] GIL and threading vs multiprocessing (ties to PyTorch DataLoader perf)
  - [ ] Memory model: refcounting, GC, `weakref` (a natural companion note)
- [ ] **Bash/shell** (day-to-day tooling) — a small `shell/` section.
- [ ] **Version control beyond basics** — interactive rebase, bisect, worktrees, submodules (useful for OSS work; see [[../OpenSource]]).

## Prioritization

1. **C++ + CMake + debugging** — unblocks compiler/GPU contribution work.
2. **Python C API + GIL** — the PyTorch-specific knowledge.
3. **git advanced** — small but immediately useful.
