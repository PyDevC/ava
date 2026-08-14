# C++ memory layout: structs, alignment, and the cache

How the compiler lays a `struct` out in memory determines its size, its cache behavior, and — in GPU/compiler work — how much bandwidth a kernel burns. This is the C++ half of the "memory is the bottleneck" story (see [cpp-templates-stl](cpp-templates-stl.md) for the locality rules and [Tensor-TensorImpl-Storage](../../PyTorch/Tensor-TensorImpl-Storage.md) for how PyTorch encodes layouts).

## The rules: alignment decides offsets

- **Alignment**: every type has an alignment (`alignof(T)`) — the required address boundary for its objects (1 for `char`, 4 for `int`, 8 for `int64_t`/pointers, 16 for `__int128`/vector types). The compiler must place each member at an offset that is a multiple of its alignment.
- **The padding consequence**: members that don't naturally line up get *padding bytes* inserted between them, and the struct's total size is rounded up to a multiple of its *largest* member's alignment (so arrays of the struct keep each element aligned).
- `sizeof(T)` ≥ sum of `sizeof(members)`; the difference is padding. `offsetof(T, member)` gives each member's real offset — use it, don't guess.

## Why member order changes size

```
struct Bad {   // char, then int, then char
    char  a;   // offset 0
    int   b;   // offset 4 (3 pad bytes after a)
    char  c;   // offset 8
};             // size 12 (rounded up from 9 to alignment 4)

struct Good {  // ints first, chars last
    int   b;   // offset 0
    char  a;   // offset 4
    char  c;   // offset 5
};             // size 8, no padding
```

Same members, 12 vs 8 bytes. In a kernel with millions of elements, that 50% bloat shows up as real bandwidth loss. The rule of thumb: **sort members by descending alignment**. This is the "which layout" decision that matters for structs copied between host and device in CUDA/HIP too (see [rocm-developers](../../Roadmaps/rocm-developers.md)).

## Forcing layout: alignas and packed

- `alignas(64)` on a member or struct raises its alignment — the classic trick for making a hot struct land on a cache-line boundary (each 64-byte line holds exactly your object).
- `alignof(T)` queries alignment; `offsetof` verifies the layout you think you designed.
- `__attribute__((packed))` (or `#pragma pack`) removes *all* padding — smaller, but unaligned member accesses can fault on strict-alignment hardware (x86 is lenient, ARM/GPU are not) and the compiler generates slower access code. Use it only for wire/disk formats.

## Cache-line awareness: the performance layer

- A cache line is 64 bytes on modern x86/ARM. Memory is moved in lines, not bytes: touching one field of a struct drags in the rest of its line.
- **False sharing**: two threads own different fields (or different objects) that share a line → every write invalidates the line for the *other* thread → cache-line ping-pong that serializes them. Fix: separate hot fields onto different lines (`alignas(64)` the boundary) — this is real in lock-free queues (see [cpp-concurrency](cpp-concurrency.md)).
- **Hot/cold split**: fields touched on every operation vs fields touched rarely. Split the struct so hot fields pack together into fewer lines and cold fields (error paths, metadata) live elsewhere. This is exactly how PyTorch structures tensor metadata (see [Tensor-TensorImpl-Storage](../../PyTorch/Tensor-TensorImpl-Storage.md)) and why AoS vs SoA changes everything for vectors of structs (see [cpp-templates-stl](cpp-templates-stl.md)).

## Why it matters

Struct layout is invisible until it isn't: a kernel that's "surprisingly slow" is often a padded AoS layout thrashing the cache, and a "random crash on ARM but not x86" is often unaligned access from a packed struct. Reading MLIR/LLVM/PyTorch struct definitions with `offsetof` in mind explains both their size and their field order.

## Related

- [cpp-memory-model](cpp-memory-model.md) — the value/move/RAII semantics these objects carry.
- [cpp-templates-stl](cpp-templates-stl.md) — container locality, AoS vs SoA.
- [cpp-concurrency](cpp-concurrency.md) — false sharing is where layout meets threads.
- [Tensor-TensorImpl-Storage](../../PyTorch/Tensor-TensorImpl-Storage.md) — PyTorch's layout vocabulary (channels-last etc.).
