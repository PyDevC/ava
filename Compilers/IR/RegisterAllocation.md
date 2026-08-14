# Register allocation

Register allocation maps the unlimited virtual variables of the IR onto the limited set of **physical registers**. It's the optimization that actually decides whether generated code is fast or spills to memory constantly.

## The framing

For each virtual variable, find a physical register such that:

- **No two simultaneously-live variables share a register** — the interference condition, computed from [Liveness](Liveness.md).
- Register pressure = number of live variables at a point. If pressure exceeds available registers, we **spill** (move values to memory).

## The classic algorithm: graph coloring

1. Build the **interference graph**: one node per virtual variable; edge between `a` and `b` iff they're live simultaneously.
2. Color the graph with K colors (K = number of registers). Adjacent nodes must differ.
3. If a node can't be colored (fewer colors than neighbors), **spill** it — keep it in memory instead of a register.

Graph coloring is NP-hard in general, so compilers use greedy heuristics (Chaitin, Briggs).

## Why SSA helps (hugely)

- SSA variables are *defined once*, and the interference graph of an SSA program is a **chordal graph** — which is optimally colorable in polynomial time. This is why modern compilers do **SSA-based register allocation** (LLVM's `RAGreedy`, the "linear scan on chordal graphs" result of Hack & Goos).
- **Linear scan** is the simpler, practical alternative: sort live ranges by start point, assign registers in a single pass, spill on overflow. Not optimal, but fast and easy to implement (used in JITs and by some fast backends).

## The two related transforms

- **Coalescing** — merge `a := b` copy pairs into the same register so the copy disappears. Must be careful: coalescing can *increase* interference and cause spills (Briggs's conservative coalescing trades this off).
- **Spill cost** — decide *which* variable to spill by how often it's used inside loops (spilling loop bodies is expensive).

## Why it matters for DL compilers

Inductor/Triton codegen is essentially register allocation + vectorization at the kernel level: "how many accumulators can I keep in registers before I spill to shared memory/global?" The occupancy and register-pressure trade-offs are the same graph-coloring story, just with block-level programming. See [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md) and [triton-compiler-engineer](../../Roadmaps/triton-compiler-engineer.md).

## Related

- [Liveness](Liveness.md) — the analysis that defines the interference graph.
- [SsaConstruction](SsaConstruction.md) — why SSA makes allocation tractable.
- [PhiElimination](PhiElimination.md) — phis must be gone (or coalesced) before allocation.
