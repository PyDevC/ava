# SSA construction

This is the complete algorithm that turns ordinary (non-SSA) code into SSA form — putting together [Dominators](SSA/Dominators.md) and [Liveness](Liveness.md). The classic reference is Cytron, Ferrante, Rosen, Wegman & Zadeck (1991).

## The two phases

**Phase 1 — Phi insertion (place the Φ functions).**

For each variable, its Φ functions go exactly at the *join nodes* where the variable needs merging. That set of nodes is computed using dominance frontiers (see [Dominators](SSA/Dominators.md)):

```
for each variable v with definition node d:
    worklist = { d }
    while worklist not empty:
        n = pop worklist
        for each m in DF[n]:            # dominance frontier of n
            if v not yet assigned a phi at m:
                place phi(v) at m
                worklist += { m }        # phi is itself a def; its frontier may need phis too
```

Why the phi defs cascade: a Φ is a definition of `v`, so it can force further Φ functions in *its* dominance frontier. This is why the worklist climbs the frontier transitively.

**Phase 2 — Renaming (give every def a unique name, rewrite uses).**

Walk the dominator tree pre-order, maintaining a stack of current versions per variable. The order inside a block matters: phi defs go on the stack **before** the block's ordinary uses are renamed, and phi operands are filled **per predecessor edge** after that predecessor finishes:

```
rename(n):
    for each phi in n:                       # phi defs first: push a fresh
        push new version v_<k> for its var   #   version BEFORE ordinary uses
    for each instruction i in n:             #   (loop-carried reads must see it)
        for each use of variable v in i:
            replace with version v_<top(stack[v])>
        if i defines variable v:
            push new version v_<k>; record def
    for each successor s of n:               # fill s's phi operands edge-by-edge
        for each phi in s whose operand is edge n→s:
            set operand = v_<top(stack[v])>  # version live at END of n
    for each child c of n in dominator tree:
        rename(c)
    pop versions introduced in this block
```

Filling phi operands at the *end* of the predecessor (from that predecessor's stack) is the non-obvious bit: when you're inside `n` you can't know the value coming into `s` from a different, not-yet-visited predecessor — so each edge's operand is resolved while its predecessor is on top of the stack.

The renaming processes blocks in **dominator-tree order** (not program order) so that every def is on the stack before the uses it dominates.

## Why it works

- Every use is dominated by the def/Φ of the version it reads (thanks to phi placement at dominance frontiers) — so the stack is always correct at a use.
- Φ operands are resolved per *predecessor edge*: the version coming from that predecessor's current value.

## Result

Unique names, Φ functions at joins, and — if you only place Φ's for *live* variables (see [Liveness](Liveness.md), [PruneSSA](SSA/PruneSSA.md)) — no dead phis.

## Related

- [Dominators](SSA/Dominators.md) — the frontier algorithm powering phase 1.
- [PruneSSA](SSA/PruneSSA.md) — minimal vs pruned SSA (pruned = liveness-aware phi insertion).
- [PhiElimination](PhiElimination.md) — the reverse problem: getting rid of Φ's before codegen.
