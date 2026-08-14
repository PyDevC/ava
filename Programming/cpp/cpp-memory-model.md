# C++ memory model: values, moves, RAII

The C++ foundations for the compiler/GPU work (and for reading PyTorch/MLIR/LLVM sources): **value semantics**, **move semantics**, and **RAII**. Getting these right is what makes C++ code correct and leak-free; getting them wrong is the source of most bugs.

## Value semantics (the first shock)

- C++ variables hold *objects*, not references: `int a = 1; int b = a;` → `b` is a **copy**. Assignment and passing **copy the value** unless you say otherwise (`&`).
- Containers copy too: `std::vector<int> v2 = v1;` deep-copies. That's why "pass by value" for big objects is a performance trap — pass `const&` instead.
- `a = b` with objects = copy-assignment (which the compiler can elide with **copy elision** in many contexts — don't rely on it).

## Move semantics

- `std::move(x)` → the object is *stolen* from (its internal pointer handed over, the source left empty/valid-unspecified). This is how `return std::vector{...}` and `std::vector<T> v = make_vec();` avoid copies — the move constructor/assignment steal the heap buffer.
- The rules: **rvalues** (`prvalue`/`xvalue`) bind to `T&&`, lvalues to `T&`. `std::move` casts lvalue → rvalue so the move constructor is chosen.
- Rule of zero/five: if you declare a destructor/copy/move constructor or assignment, you probably need all of them — or better, none (let members handle it) → "Rule of Zero".
- This is why `return bigVector;` in C++11+ is efficient (move), and why `emplace_back(std::move(x))` exists.

## RAII — the resource lifetime contract

- **RAII** = resource acquisition is initialization: acquire in the constructor, release in the destructor. When the object's scope ends (or exception unwinds), the destructor runs — deterministically, exceptions-safe.
- `std::vector`, `std::unique_ptr`, `std::lock_guard`, `std::fstream`, MLIR's `Operation`, PyTorch's `TensorImpl` all own resources via RAII.
- The alternative — manual `new`/`delete` — is where leaks and double-frees come from. **Never write bare `new`/`delete`** in modern C++; use smart pointers + containers.

## The mental model

```
value semantics:  copies are real (avoid for big objects)
move semantics:   transfers ownership (the perf trick)
RAII:             ownership = scope (the correctness trick)
```

Every C++ API you read (MLIR passes, ATen kernels, LLVM) is built on these three. Read them with this lens and the "why does this signature look like this" questions answer themselves.

## Related

- [cpp-smart-pointers](cpp-smart-pointers.md) — `unique_ptr`/`shared_ptr`/`weak_ptr` in practice.
- [Passes](../../Compilers/MLIR/Passes.md) — MLIR code that lives by these rules.
- [Tensor-TensorImpl-Storage](../../PyTorch/Tensor-TensorImpl-Storage.md) — ownership via `Storage`/refcounts.
- [cpp-debugging](cpp-debugging.md) — ASAN finds the violations of these rules.
