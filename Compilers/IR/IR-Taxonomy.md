# IR taxonomy: CFG vs graph, high vs low level, stack vs register machines

Three axes classify intermediate representations. Knowing where a given IR sits on each axis explains what passes it can run and what it's for.

## Axis 1: CFG (basic-block) vs graph IRs

- **CFG IRs** split code into *basic blocks* (straight-line sequences) joined by explicit branches. Control flow is first-class: the optimizer reasons about block ordering, dominance, and loop structure. Examples: LLVM IR, MLIR's `cf`/`scf`, classic three-address code.
  - Strengths: precise control-flow analysis (dominance, liveness — the SSA machinery in [this directory](SSA/)), register allocation, loop optimization.
  - Weakness: every branch is explicit, so high-level structure (a matmul, a fused pointwise chain) is invisible until reconstructed by pattern matching.
- **Graph IRs** represent computation as a DAG of ops with data-flow edges (tensors in, tensors out). Control flow is an afterthought — either unsupported, or special ops (e.g. FX `call_module` on submodules, `if`/`loop` subgraphs).
  - Strengths: exactly matches the shape of a neural network; graph-level rewrites (fusing a chain of pointwise ops, CSE, dead-elimination of unused outputs) are trivial because edges *are* data dependencies.
  - Weakness: no notion of sequential execution order or branches, so anything data-dependent (loops over tensor contents, shape-dependent control flow) breaks — this is literally why TorchDynamo inserts [graph breaks](../PyTorchCompiler/TorchDynamo/GraphBreaks.md).

PyTorch's FX graph is the canonical graph IR ([FX-Graph-IR](../PyTorchCompiler/FX-Graph-IR.md)); MLIR's `linalg` on tensors is a graph-like view on top of a structured CFG.

## Axis 2: high-level vs low-level

- **High-level IRs** keep domain meaning: `matmul`, `conv`, `layernorm` as single ops, tensor shapes symbolic or 1-1 with the source model. Great for domain optimizations (fusion, layout choice, operator rewriting) but too fat for codegen.
- **Low-level IRs** are close to the machine: explicit registers/virtual registers, memory ops, arithmetic on scalar/vector types. Every high-level op must be *lowered* into a sequence of low-level ones before codegen.
- The clever bit (and MLIR's whole design): one framework holds *both*, with dialects ranked by level — `tosa`/`stablehlo` (high) → `linalg` → `scf`+`vector` → `llvm` (low). You run passes at the level where the optimization is expressible, then step down ([MLIR](../MLIR/)).

## Axis 3: stack vs register machines

- **Stack machines** evaluate by pushing/popping an implicit operand stack. Every instruction says *what* to do (`add`, `mul`), not *where* the operands live. Bytecode is compact and easy to interpret/vendor. JVM bytecode and CPython bytecode are the big examples ([bytecode](../../Programming/python/core/bytecode.md)).
  - Consequence: no register allocation in the bytecode itself — the interpreter maps the stack to real registers at runtime, or a JIT does it later.
- **Register machines** name explicit (virtual) registers: `%3 = add %1, %2`. More instructions are needed to move data, but the IR is a much closer model of the target and enables SSA (a register written exactly once). LLVM IR is the archetype.
- Hybrids exist: MLIR's `cf` is a register-style IR that still supports `cf.br`; Inductor's loop IR is effectively a register-style representation of tile computations.

## Why this matters

Every DL compiler is a chain of IRs that walks down all three axes simultaneously: FX graph (graph, high) → AOTAutograd forward/backward → Inductor IR (loop-level, structured) → Triton/C++/PTX (register, low). The taxonomy tells you which optimizations are legal at each step — graph rewrites early, loop transforms mid, codegen-specific choices last.

## Related

- [SSA](SSA/) — the value-numbering scheme most register IRs use.
- [FX-Graph-IR](../PyTorchCompiler/FX-Graph-IR.md) — a concrete graph IR in the wild.
- [LLVM-IR](LLVM-IR.md) — a concrete low-level register IR.
- [bytecode](../../Programming/python/core/bytecode.md) — a concrete stack machine.
