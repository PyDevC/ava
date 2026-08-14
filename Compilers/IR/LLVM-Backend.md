# LLVM backend pipeline

Once LLVM IR is optimized, the backend turns it into machine code. The classic path is **SelectionDAG**; modern LLVM is replacing it with **GlobalISel**. The shape is: expand the IR into low-level ops → *select* instructions → schedule → allocate registers → emit `.s`.

## SelectionDAG (the classic pipeline)

1. **Build the DAG** — translate each IR instruction into a DAG of target-independent `SDNode`s (data-flow edges). `load`/`store` become chains to preserve ordering; the DAG is per basic block.
2. **Legalize** — break types and ops the target doesn't support into ones it does (`i128` add → two `i64` adds; a target without `mul` → shifts and adds). Types first, then operations.
3. **Combine (DAG combine)** — target-independent and target-specific peephole simplifications on the DAG (the backend's mini-optimizer).
4. **Select** — pattern-match DAG nodes to target instructions (patterns live in `*.td`). One IR-level op becomes zero or more machine instructions: `add` → `ADD`, a multiply-accumulate pattern may match a single `FMA`.
5. **Schedule** — order the selected instructions (list scheduling with the target's cost model). Phis are eliminated to copies first; there's a pre-RA and a post-RA scheduler.

After selection, SSA is gone: the IR is **MachineInstrs** over virtual/physical registers.

## GlobalISel (the modern replacement)

SelectionDAG is per-block and rigid. GlobalISel keeps a **MachineIR** (MIR) that is still CFG-shaped and mostly SSA, and runs:

- **IRTranslate** — IR → MIR (generic ops, still target-independent).
- **Legalize** — generic op + type legalization, same idea as SelectionDAG.
- **RegBankSelect** — assign each value a *register bank* (GPR/FPR/vector), modeling the target's register classes at IR level.
- **InstructionSelect** — table-driven pattern matching to real instructions, folding `g_copy`s as it goes.

Because it works on a proper IR with control flow, GlobalISel handles new targets (ARM, RISC-V) and can run *optimizations* in MIR — something SelectionDAG couldn't do.

## Scheduling & register allocation

- **Instruction scheduling** — reorder instructions to respect dependencies and exploit the target (latency hiding, avoiding stalls).
- **Register allocation** — exactly the theory in [RegisterAllocation](RegisterAllocation.md): build live ranges from liveness, assign physical registers (LLVM's `RAGreedy` = greedy allocation on chordal SSA interference graphs + spill machinery). Spill code is inserted where needed; coalescing folds copies.
- Then: stack layout, **prologue/epilogue** insertion (saving callee-saved registers), and emission.

## Emitting `.s`

The final step prints text assembly (or writes object code via MC). The `.s` from `clang -S` is the *end product* — every optimization decision above is visible in it.

## Why backend passes differ from IR passes

The IR passes in [LLVM-IR](LLVM-IR.md) are **target-independent and semantic** — they simplify and transform the *program* (CSE, inlining, loop opts). Backend passes are **target-specific and mechanical** — they *choose instructions, orders, and registers*. The same IR pass pipeline serves every target; the backend is re-implemented per target (adding a CPU *is* writing a backend). This is why MLIR stops at LLVM IR (see [LLVM-Dialect](../MLIR/Dialects/LLVM-Dialect.md)) and never grows per-CPU dialects.

## Related

- [LLVM-IR](LLVM-IR.md) — the input to this pipeline.
- [RegisterAllocation](RegisterAllocation.md) — the theory the allocator implements.
- [PhiElimination](PhiElimination.md) — phis get destroyed here.
- [CUDA-Kernel-Launch](../Codegen/CUDA-Kernel-Launch.md) — the same backend story at GPU scale (PTX instead of `.s`).
