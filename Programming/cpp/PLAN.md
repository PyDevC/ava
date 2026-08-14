# Programming/cpp — Plan

## Goal

The **C++ language area** — the foundations for compiler, GPU, and framework-engineering work (reading LLVM/MLIR/PyTorch/ROCm sources and writing kernels/passes).

## What's here

- [cpp-memory-model](cpp-memory-model.md) — values, moves, RAII; the ownership model everything follows.
- [cpp-smart-pointers](cpp-smart-pointers.md) — unique/shared/weak, and the raw-pointer conventions of LLVM/MLIR.
- [cpp-templates-stl](cpp-templates-stl.md) — templates/CRTP, STL containers, cache-locality.
- [cpp-debugging](cpp-debugging.md) — ASAN/UBSAN/TSAN, gdb/lldb, perf; the workflow that finds bugs.
- [cpp-cmake](cpp-cmake.md) — targets, generators, find_package; reading and writing CMake beyond the basics.

## What to add next (see also [PLAN](../PLAN.md))

- [x] Move semantics, RAII, templates/CRTP, STL performance, memory layout → [cpp-memory-model](cpp-memory-model.md) + [cpp-templates-stl](cpp-templates-stl.md)
- [x] Build systems: CMake basics (extends [CMake_Guide](../../Compilers/MLIR/CMake_Guide.md)) → [cpp-cmake](cpp-cmake.md)
- [x] Debugging: gdb/lldb, sanitizers (ASAN/TSAN), profilers (perf) → [cpp-debugging](cpp-debugging.md)
- [ ] Memory layout details (struct layout, padding, alignment, cache lines) — deeper than the templates note
- [ ] Concurrency: std::thread/atomics, lock-free basics (pairs with the GIL note in [core](../python/core/))

## Prioritization

1. **Memory model + smart pointers** — the correctness base.
2. **Templates/STL** — how the codebases you read are written.
3. **Debugging + CMake** — the tools to work in them.
