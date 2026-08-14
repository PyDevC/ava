# Prune SSA

> Minimal SSA is the SSA with fewest or almost fewest Φ() function possible, such that each unique name is assigned value only once, and in the original code the reference of that name can easily refer to that name.
> Some of the Φ functions are dead which means that they never receive the control from it's predecessor node.

Pruned SSA form is based on a simple observation: Φ functions are only needed for variables that are "live" after the Φ function.

## Minimal vs pruned SSA

- **Minimal SSA** places Φ functions at every join node in the dominance frontier where needed. This keeps names unique with the fewest Φ's possible while still being correct.
- **Pruned SSA** goes one step further: it only inserts a Φ function for a variable if that variable is actually *live* at the insertion point (i.e. its value is used later along some path). A Φ for a dead variable is wasted work — the value it selects is never read before being overwritten.

## How pruning works

1. Compute liveness information (live-in / live-out sets per block) before inserting Φ's.
2. When deciding whether a variable needs a Φ at a join block, check that the variable is live at that block. If it's dead there, skip the Φ entirely.
3. The result has strictly fewer (or equal) Φ functions than minimal SSA, and never any dead ones.

## Why it matters

- Fewer Φ functions = less work for every later pass (register allocation, coalescing, copy propagation).
- The dominance frontier from [Dominators](Dominators.md) tells you *where* to put Φ's; liveness tells you *whether* to bother.
