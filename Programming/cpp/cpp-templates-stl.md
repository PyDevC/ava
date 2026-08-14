# C++ templates and STL performance

Templates are C++'s compile-time metaprogramming; the STL is the library built on them. Together they define what "fast idiomatic C++" looks like — and what's actually slow.

## Templates: the compile-time model

- **Function templates** (`template<typename T> T add(T a, T b)`): instantiated *per type* at compile time — zero runtime dispatch, the generated code is as specific as hand-written per-type code.
- **Class templates** (`std::vector<T>`), **non-type params** (`std::array<int, N>` — the size is part of the type), **template deduction** (`std::sort(v.begin(), v.end())` deduces from iterators).
- **CRTP** (Curiously Recurring Template Pattern): `template<class Derived> struct Base { Derived& self() { return static_cast<Derived&>(*this); } }` — inject base-class behavior that calls *derived* methods *without virtual dispatch* (compile-time polymorphism). LLVM/MLIR use it everywhere (`PassWrapper<MyPass, ...>`, see [Passes](../../Compilers/MLIR/Passes.md)).
- **The cost**: long compile times + template-bloat (code duplication per instantiation) + unreadable error messages. `concepts` (C++20) tame the errors and interfaces.

## The STL performance rules

- **Know your containers**: `vector` (contiguous, cache-friendly, O(1) amortized push_back) is the default. `list`/`map`/`set` are *node-based* (pointer chasing, cache-hostile) — a `map` is not a magic fast thing. `unordered_map` = hash (fast, but collisions + iteration cost). `deque`, `string_view` (zero-copy views, like memref's stride views in [Tensor-memref](../../Compilers/MLIR/Dialects/Tensor-memref.md)).
- **Reserve before you fill**: `v.reserve(n)` avoids the geometric-growth copies.
- **Move, don't copy** (see [cpp-memory-model](cpp-memory-model.md)): `std::move` into containers, `emplace_back` instead of `push_back(T{...})`.
- **`const` references over values** in signatures — the #1 perf bug in "average C++" (copies on every call).
- **Locality beats everything**: a `vector` of structs (SoA in numeric code: separate arrays per field) blows away a `list` or an AoS `vector` for cache-bound loops — the same reason channels-last layouts matter (see [Tensor-TensorImpl-Storage](../../PyTorch/Tensor-TensorImpl-Storage.md)).

## The "it's C++ so it's fast" trap

- Unchecked copies, `std::function` in hot loops (heap alloc + type erasure), `shared_ptr` churn, `map` where a `vector`+linear scan wins for small N. Measure (see [cpp-debugging](cpp-debugging.md) — `perf`), then fix the real hot spot.
- `-O2` is not "templates make it magic"; the optimizer does well but can't fix a quadratic algorithm.

## Related

- [cpp-memory-model](cpp-memory-model.md) — the semantics templates build on.
- [cpp-smart-pointers](cpp-smart-pointers.md) — the ownership vocabulary in templates.
- [Passes](../../Compilers/MLIR/Passes.md) — CRTP in the wild (`PassWrapper`).
- [Tensor-memref](../../Compilers/MLIR/Dialects/Tensor-memref.md) — views, the `string_view`-style idea.
