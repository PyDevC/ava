# Programming/cpp — Plan

## Goal

The **C++ language area** — the foundations for compiler, GPU, and framework-engineering work (reading LLVM/MLIR/PyTorch/ROCm sources and writing kernels/passes).

## What's here

- [[cpp-memory-model]] — values, moves, RAII; the ownership model everything follows.
- [[cpp-smart-pointers]] — unique/shared/weak, and the raw-pointer conventions of LLVM/MLIR.
- [[cpp-templates-stl]] — templates/CRTP, STL containers, cache-locality.
- [[cpp-debugging]] — ASAN/UBSAN/TSAN, gdb/lldb, perf; the workflow that finds bugs.
- [[cpp-cmake]] — targets, generators, find_package; reading and writing CMake beyond the basics.

## What to add next (see also [[../PLAN]])

- [x] Move semantics, RAII, templates/CRTP, STL performance, memory layout → [[cpp-memory-model]] + [[cpp-templates-stl]]
- [x] Build systems: CMake basics (extends [[../Compilers/MLIR/CMake_Guide]]) → [[cpp-cmake]]
- [x] Debugging: gdb/lldb, sanitizers (ASAN/TSAN), profilers (perf) → [[cpp-debugging]]
- [ ] Memory layout details (struct layout, padding, alignment, cache lines) — deeper than the templates note
- [ ] Concurrency: std::thread/atomics, lock-free basics (pairs with the GIL note in [[../python/core]])

## Prioritization

1. **Memory model + smart pointers** — the correctness base.
2. **Templates/STL** — how the codebases you read are written.
3. **Debugging + CMake** — the tools to work in them.
