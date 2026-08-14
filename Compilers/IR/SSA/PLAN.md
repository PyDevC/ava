# IR/SSA — Plan

## Goal

Notes on **SSA (Static Single Assignment) form** and its supporting analyses — the IR theory at the heart of every compiler I study.

## What's here

- [[Dominators]] — dominance, immediate dominators, the dominance-frontier algorithm.
- [[PruneSSA]] — minimal vs pruned SSA, liveness-based phi insertion.
- [[../Liveness]] — the analysis behind pruned SSA.
- [[../SsaConstruction]] — phi placement + renaming, completing the two notes above.
- [[../PhiElimination]], [[../DeadCodeElimination]], [[../CommonSubexpressionElimination]], [[../RegisterAllocation]] — the SSA-driven pipeline that follows.

## What to add next (see also [[../PLAN]])

- [x] SSA construction (phi placement + renaming) — completes both existing notes
- [x] Liveness analysis (live-in/live-out) — the analysis [[PruneSSA]] references
- [x] Phi elimination (parallel copies, coalescing)
- [x] DCE/CSE on SSA — concrete examples
- [x] Register allocation (graph coloring / linear scan) and why SSA helps
- [ ] Copy propagation on SSA — the trivial case SSA makes free
- [ ] IR taxonomy note — CFG vs graph IRs, stack vs register machines (parent plan)
