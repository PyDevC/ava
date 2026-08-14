# LLVM dialect

The `llvm.*` dialect is the **bottom of the MLIR ladder** — the place where MLIR's type system hands off to LLVM's. Lowering to LLVM IR is the last step before actual machine code.

## What it is

MLIR ops named `llvm.*` map 1:1 onto LLVM IR instructions, and MLIR types map onto LLVM types:

```
llvm.mlir.global internal @g : !llvm.array<3 x f32>
%ptr = llvm.mlir.addressof @g : !llvm.ptr<array<3 x f32>>
%v   = llvm.load %ptr : !llvm.ptr<f32>          // → llvm load f32* %ptr
```

- `!llvm.ptr`, `!llvm.func`, `!llvm.struct<...>` — LLVM's types as MLIR types.
- Ops: `llvm.load`, `llvm.store`, `llvm.add`, `llvm.call`, `llvm.br`, `llvm.getelementptr` (the infamous GEP), `llvm.intr.*` (intrinsics like `llvm.intr.llvm.memcpy`).

## The conversion path

```
memref<...>  →(convert-to-llvm)→  !llvm.ptr + struct (descriptor)
cf.br        →  llvm.br
arith.addi   →  llvm.add
vector.add   →  llvm.add on vector types (or intrinsics)
```

The memref→LLVM lowering is where descriptors become real: a memref becomes a struct {ptr, offset, size[], stride[]} or a bare pointer depending on layout convention — this is the *actual* ABI choice (callable-from-C++ vs opaque). `-convert-to-llvm` is the modern catch-all that does all of this in one pass.

## Why it matters

- MLIR deliberately does NOT have a dialect for every CPU instruction — it stops at LLVM IR and lets LLVM's backend (SelectionDAG/GlobalISel, scheduling, register allocation — see [RegisterAllocation](../../IR/RegisterAllocation.md)) do the rest. MLIR adds value *above* LLVM; LLVM is the rock below.
- Interop: a dialect that lowers to LLVM can be JIT'd, AOT'd, and called from C/C++/Python — the whole "compiler in a library" story.

## Related

- [Builtin-Dialects](../Builtin-Dialects.md) — `arith`/`memref`/`func` are the dialects that feed this one.
- [RegisterAllocation](../../IR/RegisterAllocation.md) — what happens after LLVM IR.
- [TorchInductor](../../DLCompilers/TorchInductor/TorchInductor.md) — Inductor's `cpp`/CUDA backends play this role without MLIR.
- [CMake-Guide](../CMake-Guide.md) — linking MLIR into a project that then lowers to LLVM.
