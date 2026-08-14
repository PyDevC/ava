# MLIR operations, types, values

MLIR's IR is a **typed, nested SSA graph**. Everything is a node in one uniform structure — one `Operation`, one `Type` system, one attribute mechanism — which is what lets a single infrastructure host every dialect from `affine` to `llvm`.

## The three primitives

- **Operation** (`mlir::Operation`): a node. Has a name (`toy.transpose`, `arith.addi`), operand *values*, results, **attributes** (compile-time constants like `{dense<...> : tensor<2x2xf64>}`), **regions** (nested ops), and *traits/interfaces* (properties like "commutative", "has side effects"). Ops are defined in TableGen/ODS (see [TableGen](TableGen.md)).
- **Type** (`mlir::Type`): the SSA value type — `i32`, `f64`, `index`, `tensor<2x3xf64>`, `memref<2x3xf64>`, dialect-defined types too. Each type can carry structure (shape, layout) — the key to the high-level→low-level story ([Tensor-memref](Dialects/Tensor-memref.md)).
- **Value** (`mlir::Value`): the *result* of an op (a definition) or a block argument. SSA: every value is defined exactly once (see [Dominators](../IR/SSA/Dominators.md) for the SSA theory). MLIR value is the *typed* handle the IR threads through operands.

```
%0 = arith.constant dense<2.0> : tensor<2x2xf64>   // op, produces %0: Value
%1 = linalg.add %0, %0 : tensor<2x2xf64>           // consumes %0, produces %1
```

## Attributes vs operands

- **Attribute**: compile-time constant, not a runtime SSA value — shapes, strides, the `i8` in `affine.for`'s step, a dense tensor of weights. The optimizer can inspect/rewrite them freely; they carry the *metadata* the framework needs to prove things (sizes, layouts, integer constants for constant folding).
- **Operand**: a runtime value flowing between ops.

## Why this uniform design matters

- One **pass framework** (see [Passes](Passes.md)), one **pattern engine** ([DialectConversion](DialectConversion.md)), one printer/parser — and a "dialect" is just a namespace of ops + types that plug into it. That's the "infrastructure" half of MLIR's pitch: you build a language by defining ops, and *everything else is free*.

## Related

- [Affine](Dialects/Affine.md) — an example dialect exercising all three primitives.
- [TableGen](TableGen.md) — where ops and types are actually declared.
- [Dominators](../IR/SSA/Dominators.md) — the SSA theory behind `Value`.
- [Regions](Regions.md) — what ops can hold *inside* them.
