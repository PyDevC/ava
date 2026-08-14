# Common subexpression elimination (CSE)

CSE finds computations that *always* produce the same value and replaces re-computation with a reference to the first result. If `a*b` appears twice with the same `a` and `b` and neither changed in between, the second is redundant.

## The key question: when is it safe?

A subexpression `f(x)` can be reused at a later point only if:

1. **The operands are unchanged** — no `def` of `x` (or aliased memory) between the two occurrences.
2. **`f` is pure** — same inputs → same output, no side effects. Loads are *not* pure (memory could change underneath); that's why compilers distinguish CSE (expressions) from *load elimination / GVN* (memory).

## How it's implemented

**Hash-based (the practical way):** maintain a hash table of "expression → value". Walk instructions in order; when you see `a*b`:

- if the hash table already has `(a,b,*)` with a still-valid def → replace this instruction with a use of the stored value;
- else → hash it and continue.

The hashing gives you *value numbering*-like behavior without full equivalence analysis. **Global value numbering (GVN)** generalizes this: two expressions are equivalent if they compute the same value, even when syntactically different (`a+b` vs `b+a`, or `x*1` vs `x`).

## In SSA form

SSA (see [SSAConstruction](SSAConstruction.md)) makes CSE nearly trivial for *dominance-scalar* expressions: a def dominates every use, so a value is available at a use exactly when its def dominates it. The check "is the earlier computation still valid here" becomes a dominator-tree query instead of a dataflow analysis.

## CSE in ML frameworks

This matters in DL compilers too: model graphs re-compute common subgraphs (shared weights, repeated layer patterns), and graph-level CSE on the FX/ONNX graph is a standard "graph simplification" pass — see [DeadCodeElimination](DeadCodeElimination.md)'s sibling role and the DL compiler pipeline in [DLCompilers](../DLCompilers/).

## Related

- [DeadCodeElimination](DeadCodeElimination.md) — CSE creates the redundancy, DCE removes the leftovers.
- [Liveness](Liveness.md) / [Dominators](SSA/Dominators.md) — the analyses CSE leans on in SSA form.
- [Dominators](SSA/Dominators.md) — the availability check under the hood.
