# tensor and memref: the two value models

MLIR deliberately has **two ways to describe an array of data**, and the entire lowering story is the journey between them.

## `tensor` — immutable SSA value

`tensor<2x3xf64>` is a *value*: pure data, no location, no aliasing. Like a NumPy array that can never be mutated in place.

- Ops on tensors are functional: `tensor.extract_slice`, `tensor.insert_slice`, `tensor.concat` — every "modification" creates a new value. That immutability is what makes **reordering and fusion legal for free** (SSA reasoning: if nothing can alias, you can move ops around, see [[../IR/SSA/Dominators]]).
- No memory exists yet — a tensor is an abstraction.

## `memref` — mutable memory descriptor

`memref<2x3xf64, strided<[3,1], offset: 0>>` is a **pointer-ish descriptor**: the memory, the shape, the strides, the memory space. Mutable, aliasable, real.

- `memref.load/store` read/write in place; `memref.subview` creates a new descriptor sharing the same buffer (zero-copy slicing).
- Aliasing breaks SSA's clean reasoning — the optimizer must now think about writes.

## Bufferization — the crossing

**Bufferization** rewrites `tensor`-world ops into `memref`-world ops (allocations + stores). It's a *pass* (`-one-shot-bufferize`), and it decides the alloc/ownership strategy. After it, the IR is "real": memory exists, aliases appear, and the remaining pipeline (→ vector → llvm, see [[scf-vector]]) never has to undo it.

The trade it encodes:

| | tensor | memref |
|---|---|---|
| Aliasing | none (pure values) | yes |
| Reordering | free (SSA) | must respect W/W, W/R hazards |
| Maps to hardware | no | yes |
| Memory allocation | n/a | alloc/free decisions |

## Why both exist

High-level optimization wants *values* (easy reasoning); codegen needs *memory* (hardware reality). Two value models let each stage operate in the world it's best in, with bufferization as the single, well-defined transition. This is the heart of the "two-level IR" claim — and it's why IREE/Inductor can fuse aggressively at the tensor level then generate tight memref/vector code.

## Related

- [[Builtin-Dialects]] — `tensor`/`memref` are builtin types.
- [[linalg]] — the ops that live on the tensor→memref boundary.
- [[scf-vector]] — the codegen side after bufferization.
- [[Regions]] — nesting + these value models = the full MLIR picture.
