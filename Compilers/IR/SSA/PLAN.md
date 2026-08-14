# IR/SSA — Plan

## Goal

Notes on **SSA (Static Single Assignment) form** and its supporting analyses — the IR theory at the heart of every compiler I study.

## What's here

- [Dominators](Dominators.md) — dominance, immediate dominators, the dominance-frontier algorithm.
- [PruneSSA](PruneSSA.md) — minimal vs pruned SSA, liveness-based phi insertion.
- [Liveness](../Liveness.md) — the analysis behind pruned SSA.
- [SsaConstruction](../SsaConstruction.md) — phi placement + renaming, completing the two notes above.
- [PhiElimination](../PhiElimination.md), [DeadCodeElimination](../DeadCodeElimination.md), [CommonSubexpressionElimination](../CommonSubexpressionElimination.md), [RegisterAllocation](../RegisterAllocation.md) — the SSA-driven pipeline that follows.

## What to add next (see also [PLAN](../PLAN.md))

- [x] SSA construction (phi placement + renaming) — completes both existing notes
- [x] Liveness analysis (live-in/live-out) — the analysis [PruneSSA](PruneSSA.md) references
- [x] Phi elimination (parallel copies, coalescing)
- [x] DCE/CSE on SSA — concrete examples
- [x] Register allocation (graph coloring / linear scan) and why SSA helps
- [ ] Copy propagation on SSA — the trivial case SSA makes free
- [ ] IR taxonomy note — CFG vs graph IRs, stack vs register machines (parent plan)
