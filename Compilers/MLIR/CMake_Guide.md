# CMake Guide

This is Guide to properly write CMakeLists.txt file and structure it for mlir based projects

## Finding and linking MLIR

MLIR installs a proper CMake config package, so you should never hard-code paths:

```cmake
find_package(MLIR REQUIRED CONFIG)
list(APPEND CMAKE_MODULE_PATH "${MLIR_CMAKE_DIR}")
include(AddMLIR)
```

`AddMLIR.cmake` (shipped with MLIR) provides the helpers `add_mlir_library`, `add_mlir_dialect`, `add_mlir_doc`, etc.

## Basic structure for a dialect

```cmake
# TableGen generates ODS boilerplate (op definitions, types, interfaces)
set(LLVM_TARGET_DEFINITIONS MyDialect.td)

mlir_tablegen(MyDialectOps.h.inc -gen-op-decls)
mlir_tablegen(MyDialectOps.cpp.inc -gen-op-defs)
add_public_tablegen_target(MyDialectOpsIncGen)

add_mlir_dialect_library(MLIRMyDialect
  MyDialect.cpp
  MyDialectOps.cpp

  DEPENDS
  MyDialectOpsIncGen

  LINK_LIBS PUBLIC
  MLIRIR
  MLIRSupport
)
```

## Key points

- `find_package(MLIR REQUIRED CONFIG)` — always do this, MLIR doesn't play well with manual path hacking.
- `LLVM_TARGET_DEFINITIONS` + `mlir_tablegen(...)` — ODS `.td` files are processed at build time by `mlir-tblgen` into `.inc` headers.
- `add_public_tablegen_target(...)` — makes the generated files a proper CMake target so they're built before anything that includes them.
- `add_mlir_dialect_library(...)` links against core MLIR libraries (`MLIRIR`, `MLIRSupport`, ...) with the right PIC/build flags.
- For a standalone project that also includes LLVM you may need `find_package(LLVM REQUIRED CONFIG)` too, and to handle `${LLVM_LIBRARY_DIR}` in `LINK_PATH`.

## Pitfalls

- Don't `add_library` raw `.cpp` files that include generated `.inc` — you'll get "file not found" because tablegen never ran.
- Keep `MLIR_DIR` / `LLVM_DIR` pointed at the correct build or install tree — mixing versions of LLVM and MLIR is the #1 source of weird CMake errors.
