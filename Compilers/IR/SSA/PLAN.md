# IR/SSA — Plan

## Goal

Notes on **SSA (Static Single Assignment) form** and its supporting analyses — the IR theory at the heart of every compiler I study.

## What's here

- [[Dominators]] — dominance, immediate dominators, the dominance-frontier algorithm.
- [[PruneSSA]] — minimal vs pruned SSA, liveness-based phi insertion.

## What to add next (see also [[../PLAN]])

- [ ] SSA construction (phi placement + renaming) — completes both existing notes
- [ ] Liveness analysis (live-in/live-out) — the analysis [[PruneSSA]] references
- [ ] Phi elimination (parallel copies, coalescing)
- [ ] DCE/CSE on SSA — concrete examples
- [ ] Register allocation (graph coloring / linear scan) and why SSA helps
