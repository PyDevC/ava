# LLVM IR

LLVM IR is the low-level **register-style IR** between a compiler's frontend and its backend: typed, explicit, SSA, and (mostly) target-independent. Every serious compiler I care about — including MLIR at its bottom and Triton on GPU — either *is* LLVM IR or lowers to it.

## The three formats

The same IR exists in three equal forms:

- **Bitcode (.bc)** — binary, fast to serialize/deserialize (the compiler's interchange format).
- **Text (.ll)** — human-readable, what `opt`/`llvm-dis` show you.
- **In-memory** — the `Module`/`Function`/`BasicBlock`/`Instruction` C++ objects passes mutate.

The three convert freely (`llvm-as`/`llvm-dis`), which is why I can *read* what any compiler produces.

## Structure

```
Module              (globals, function decls+defs, metadata)
 └─ Function        (signature, a list of BasicBlocks)
     └─ BasicBlock  (straight-line; ends in a terminator)
         └─ Instruction (typed, SSA: result defined exactly once)
```

- Every value is **typed**: `i32`, `i64`, `float`, `ptr`, `[4 x i32]`, `{i32, i64}`. There's no untyped "register".
- Values are in **SSA form** (`%x = add i32 %a, %b`), so joins go through `phi` nodes.
- No implicit control flow: each basic block ends in a **terminator** (`br`, `ret`, `switch`, `unreachable`).

## The instruction set (the ones I actually see)

```
%p  = alloca i32                         ; stack slot for a value
store i32 %v, ptr %p                    ; write
%w  = load i32, ptr %p                   ; read
%e  = getelementptr i32, ptr %arr, i64 %i   ; address of arr[i] (GEP — pointer arithmetic, NOT a deref)
%r  = call i32 @foo(i32 %x)
br  label %next                          ; or br i1 %c, label %t, label %f
%c  = icmp slt i32 %a, %b                ; integer compare → i1
%f  = fcmp olt float %a, %b              ; float compare (with NaN semantics)
```

Plus arithmetic (`add/sub/mul/udiv/sdiv/fdiv`...), bitwise ops, `select`, memory fences, and *intrinsics* (`llvm.memcpy`, `llvm.lifetime.start`) — pseudo-ops passes understand and the backend expands.

GEP trips everyone: `getelementptr` computes an *address*, it does not load. Its type carries the element type; the result is always a `ptr`.

## The pass manager

Passes mutate the in-memory IR in place. The classic `legacy::PassManager` is being replaced by the **new pass manager**:

- `FunctionPassManager` — runs per-function passes (most optimizations live here).
- `ModulePassManager` — module-wide passes (globaldce, inlining across functions, LTO-style work).
- Nested: a module PM contains a function PM (an adaptive scheduler: inline, run the function pipeline, thin out).

On the command line:

```
opt -passes='default<O2>' file.ll
opt -passes='function(mem2reg,instcombine),loop(loop-rotate,loop-unroll),simplifycfg' file.ll
```

## Canonical passes (what they do to the IR)

- **mem2reg** — promote `alloca`+`load`+`store` to SSA registers (inserting `phi`). This is the *whole point* of the "SSA with a hat" approach: frontends emit allocas and let mem2reg build real SSA. See [SsaConstruction](SsaConstruction.md).
- **instcombine** — the catch-all algebraic simplifier (like MLIR canonicalization, see [Canonicalization](../MLIR/Canonicalization.md)): `x+0 → x`, strength reduction, constant folding. Runs constantly, between almost everything.
- **loop-rotate** — turns a `while` loop into a `do-while` (test at the bottom), making the body straight-line and enabling later transforms.
- **simplifycfg** — CFG cleanup: merge identical blocks, drop empty/unreachable blocks, convert branch-on-branch to goto.

## Why it matters

Everything lowers *into* LLVM IR and the backend runs *from* it: `clang -S -emit-llvm`, MLIR's [LLVM dialect](../MLIR/Dialects/LLVM-Dialect.md), Triton's frontend. Knowing it well means I can read any `.ll`/`.bc` a tool dumps and reason about what `opt`/passes did.

## Related

- [LLVM-Backend](LLVM-Backend.md) — what happens to the IR after the passes.
- [IR-Taxonomy](IR-Taxonomy.md) — LLVM IR as the archetypal low-level register IR.
- [RegisterAllocation](RegisterAllocation.md) — the theory the backend applies.
- [LLVM-Dialect](../MLIR/Dialects/LLVM-Dialect.md) — MLIR's 1:1 mapping onto this IR.
