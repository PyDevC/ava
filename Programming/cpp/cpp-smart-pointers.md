# C++ smart pointers

`unique_ptr`, `shared_ptr`, `weak_ptr` — the ownership vocabulary of modern C++ (see [[cpp-memory-model]] for the value/move/RAII base). Smart pointers make ownership *explicit in the type*: you can read a signature and know who owns what.

## unique_ptr — exclusive ownership

- `std::unique_ptr<T>` — the *only* owner. Movable, not copyable (copy = two owners = bug). Destructor deletes automatically (RAII).
- `std::make_unique<T>(args)` — preferred over `new` (exception-safe, no raw pointer in flight).
- The default choice: unique ownership is the simplest correct model. `std::vector`, `std::map` already own their elements this way internally.

## shared_ptr — shared ownership

- `std::shared_ptr<T>` — reference-counted (see [[python-memory]] for the same idea in Python!). Copies increment the count; last copy out → delete.
- Cost: refcount ops are atomic (thread-safe), and the control block is a second allocation. Overuse = slow + circular-lifetime bugs.
- **Prefer `unique_ptr`** unless ownership is genuinely shared. `shared_ptr` everywhere is the "everything is a singleton" anti-pattern.

## weak_ptr — the cycle breaker

- `std::weak_ptr<T>` — a *non-owning* view into a `shared_ptr`; `lock()` returns a temporary `shared_ptr` or null if the object died.
- Use for: caches, observer lists, back-references — exactly where Python uses `weakref` (see [[python-memory]]). Without it, shared-ownership cycles leak forever.

## The modern rules

1. `make_unique`/`make_shared` for creation.
2. Parameters: `const T&` or `T*` for observers (no ownership), `unique_ptr<T>` by value for ownership *transfer*, `shared_ptr` only when sharing.
3. `T*` return that doesn't own → return `T*` or `T&` (or `std::optional`); never return a raw owning pointer.
4. **Raw pointers in the wild** (MLIR/LLVM style): many C++ codebases (LLVM, MLIR) use raw pointers + explicit ownership because they predate or deliberately avoid smart pointers (performance, C++98 heritage). Read those with the convention in mind — raw pointer doesn't mean "not owned", it means "ownership is documented elsewhere" (see [[Compilers/MLIR/Operations-Types-Values]] — `Operation*` owned by the parent).

## Related

- [[cpp-memory-model]] — the semantics these encode.
- [[cpp-debugging]] — leaks/use-after-free detection.
- [[PyTorch/Tensor-TensorImpl-Storage]] — refcounted `Storage` as `shared_ptr`-like.
- [[python-memory]] — the Python analog.
