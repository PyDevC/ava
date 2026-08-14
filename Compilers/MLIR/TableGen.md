# TableGen and ODS (Operation Definition Specification)

MLIR ops and types are declared in **`.td` files** — TableGen records annotated with MLIR's ODS. `mlir-tblgen` then generates the C++ implementation (declarations, verifiers, parsers, builders) for you. This is how "define an op in one place, get a working dialect" works.

## The minimal op definition

```tablegen
def AddOp : Op<Toy_Dialect, "add", [Pure, SameOperandsAndResultType]> {
  let summary = "elementwise add";
  let arguments = (ins TensorOf<[F64]>:$lhs, TensorOf<[F64]>:$rhs);
  let results = (outs TensorOf<[F64]>:$result);
  let assemblyFormat = "$lhs `,` $rhs attr-dict `:` type($result)";
  let hasFolder = 1;
}
```

Key pieces:
- `Op<Toy_Dialect, "add", [traits]>` — dialect, op name (`toy.add`), and **traits** (`Pure` = no side effects, `SameOperandsAndResultType`, `OneRegion`...).
- `arguments`/`results` — the operand+attribute list and result types, with `$name` bindings.
- `assemblyFormat` — the custom textual syntax (or let ODS generate a default `attr-dict` printer).
- Extra hooks: `hasVerifier`, `hasFolder`, `hasCanonicalizer` (see [Canonicalization](Canonicalization.md)), `hasCustomAssemblyFormat`.

## What `mlir-tblgen` generates

Run with the ODS: `mlir-tblgen -gen-op-decls -gen-op-defs`:
- `.h.inc` — the C++ class decl (`ToyAddOp`) + `build` overloads.
- `.cpp.inc` — the `parse`/`print`, `verify`, trait implementations.
- `-gen-dialect-decls/-defs` — the dialect class; `-gen-type-decls/-defs` — custom types; `-gen-rewriters` — DRR rewrites (see [TableGen](TableGen.md) note).

The generated files are `#include`d into a `.h/.cpp` you write that just instantiates/registers them. This is the `.inc` include pattern — see [CMake-Guide](CMake-Guide.md) for the `mlir_tablegen` + `add_mlir_library` wiring that produces these.

## Why ODS instead of hand-written C++

- **Single source of truth**: decls, builders, verifiers, parse/print, traits all derived from one `def` — no drift between them.
- **Traits are free correctness**: `Pure` gives DCE/ordering guarantees; `SameOperandsAndResultType` auto-generates type verification.
- ODS is the API surface reviewers look at first — a new dialect reads like a spec, not C++.

## Gotchas

- Generated file **names must match the `#include`** exactly (`.h.inc` vs `.inc` confusion is a classic CMake error — see [CMake-Guide](CMake-Guide.md)).
- `mlir-tblgen` runs **at build time**: edit `.td` → rebuild → regenerate. There's no source-to-source refresh; the `.inc` files are build artifacts.
- Not everything is expressible in ODS: custom `parse`/`print`, unusual verifiers → opt out with `hasCustomAssemblyFormat`/`hasVerifier`.

## Related

- [TableGen](TableGen.md) — DRR rewrites on top of ODS.
- [CMake-Guide](CMake-Guide.md) — the build wiring.
- [scf-vector](Dialects/scf-vector.md) — real dialect ops to crib from.
- [Operations-Types-Values](Operations-Types-Values.md) — what the generated ops *are*.
