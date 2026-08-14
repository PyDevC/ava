# Builtin dialects: arith, func, memref

The `builtin` dialect is the core grammar (`module`, `func`, `i32`, `tensor`, `memref`...). On top of it sit the three dialects you see in every pipeline.

## `arith` — arithmetic

Scalar ops with **type-polymorphic names**: `arith.addi` (int add), `arith.addf` (float add), `arith.mulf`, `arith.cmpi`/`cmpf`, `arith.constant`. Why separate `i`/`f` suffixes? The op's legality depends on the type, and the naming keeps the verifier + type inference trivial. This is the dialect that *everything* eventually decomposes into.

## `func` — functions

`func.func @name(%arg : i32) -> i32 { ... }` with an entry block and a terminator (`func.return`). Prefixed `func.` because `builtin` is bare-namespace. Function calls: `func.call @f(%0)`. Used to be `builtin.func`; the `func` dialect now owns it (still commonly written `func.func`).

## `memref` — memory descriptors

`memref<2x3xf64, strided<[3, 1], offset: 0>>` — a **description of memory**: element type, shape, memory layout (strides), memory space. Ops: `memref.alloc`, `memref.load`, `memref.store`, `memref.subview`.

- The key idea: memref separates *"what the data is"* (shape/type) from *"where/how it lives"* (strides/space). A slice isn't a copy — it's a new descriptor with different strides/offset (zero-copy, like NumPy views).
- `memref` is where the "[Tensor-memref](Dialects/Tensor-memref.md)" story bottoms out: high-level `tensor` values are immutable SSA values; `memref` is explicit mutable memory with aliasing — the thing that actually maps to hardware.

## The roles in a pipeline

```
tensor/scf/linalg   →  (bufferize)   →  memref  →  (lower)  →  llvm / GPU
```

High-level: values, immutability, SSA-easy optimization. After **bufferization**, everything is memref (addressable memory). Then `memref` ops lower to LLVM pointers. That single split is MLIR's "two-level" story in practice.

## Related

- [Operations-Types-Values](Operations-Types-Values.md) — the op/type model these dialects use.
- [Tensor-memref](Dialects/Tensor-memref.md) — the value-model split.
- [Passes](Passes.md) — `-convert-arith-to-llvm` and friends.
- [scf](Dialects/scf-vector.md) — the control-flow dialect that `arith` computes inside.
