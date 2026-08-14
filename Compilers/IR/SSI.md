# Static single information (SSI)

SSA says **each variable's value is defined exactly once**. SSI (Static Single Information) is its mirror image: **each variable's value is used by exactly one "use" version**. Every point where a value could be consumed gets a fresh name, so information about which use is which is encoded directly in the IR.

## SSA vs SSI, side by side

- SSA renames at **definitions** — one def per name, many uses.
- SSI renames at **uses** — one use per name, many defs: the same value is re-bound to a new version for each consumer.

The classical construction: take SSA and, at every point where a variable is *used*, split it into a fresh version that only that use reads. If the use is mid-block, split the block there; if the use is at the end of a block, rename at the block edge. This is liveness ([Liveness](Liveness.md)) made structural.

## Phi vs sigma

SSA's merge function is the **φ (phi)**: `v = φ(v₁, v₂)` picks among the versions arriving along different predecessors. SSI adds its dual, the **σ (sigma)**:

```
v = ...
v1 = σ(v)     // same value, re-bound for consumer 1
v2 = σ(v)     // same value, re-bound for consumer 2
```

σ looks like a copy but carries *information*: "this exact use happened" becomes syntax rather than something an analysis has to rediscover.

## What SSI buys

- **Interference information for register allocation** — if a value's live ranges through its σ copies are disjoint, the σ's are free coalesces and the variable can reuse one register. Under the right conditions SSI makes the interference graph chordal — the same property [SSA](SSA/) gives allocation (see [RegisterAllocation](RegisterAllocation.md)).
- **Array SSA for loop transformations** — a store to an array element isn't a new def in scalar SSA, which hides data dependences. SSI applied to memory gives each store a version, so loop fusion/distribution can be done with the dependences visible as σ-style edges.
- **Facts as syntax** — "this use is the same value as that def" and "this use happens after that store" stop being analyses and become properties of the naming.

## Status: research IR vs SSA's dominance

SSA is universal — LLVM, MLIR, every serious optimizer. SSI is real but niche: it lives in the literature (Ananian's original SSI work, the e-SSA/SSI-based register allocation papers) and in specialized tools (some array and constant-propagation analyses), but no mainstream compiler exposes σ functions in its IR. I treat it as the *dual concept* that explains why SSA is so good and what the use-side facts SSA alone can't state would look like.

## Why it matters

For me the value is conceptual: SSA is half the picture. Asking "where is this value *defined* once" is SSA; asking "where is this value *used* once" is SSI, and some optimizations (array dependence, copy coalescing, some constant propagation) are dramatically cleaner in the SSI frame. Knowing the dual exists makes SSA's strengths and limits legible.

## Related

- [SSA](SSA/) — the thing SSI is dual to; [SSAConstruction](SSAConstruction.md) for how SSA is actually built.
- [Liveness](Liveness.md) — SSI's use-splitting is liveness made structural.
- [RegisterAllocation](RegisterAllocation.md) — the chordality argument SSI exploits.
- [IR-Taxonomy](IR-Taxonomy.md) — where SSA-style register IRs sit among IR designs.
