# Compilers/IR — Plan

## Goal of this directory

Understand **intermediate representations**: why compilers use them, the theory (SSA, dominance, liveness), and how IR design choices enable (or block) optimizations. This is the "why" behind everything in [[../PLAN]].

## What's already here

- **SSA/Dominators.md** — dominance, immediate dominators, the dominance-frontier algorithm.
- **SSA/PruneSSA.md** — minimal vs pruned SSA, liveness-based phi insertion.
- **Liveness.md** — live-in/live-out dataflow equations, backward flow, uses (regalloc, pruning).
- **SsaConstruction.md** — the full algorithm: phi placement via dominance frontiers + renaming.
- **PhiElimination.md** — parallel copies, the swap problem, cycle resolution, coalescing.
- **DeadCodeElimination.md** — use-count and liveness-based DCE, the side-effect rule.
- **CommonSubexpressionElimination.md** — hash-based CSE, GVN, the dominance availability check.
- **CopyPropagation.md** — name substitution on def-use chains; why SSA makes it free.
- **RegisterAllocation.md** — graph coloring, spills, linear scan, why SSA → chordal graphs.

## What to add next

- [x] **SSA construction** — the full algorithm: phi placement using dominance frontiers, then renaming (the piece both current notes set up but don't complete).
- [x] **Phi elimination** — copy insertion, `PHI` → parallel copies, how backends get rid of phis (needed before register allocation).
- [x] **Liveness analysis** — live-in/live-out, dataflow equations; the analysis PruneSSA refers to. This is the #1 missing topic.
- [x] **Register allocation** — basic graph-coloring approach and why SSA makes it nicer (chordal graphs / linear scan).
- [ ] **IR taxonomy**:
  - [ ] CFG (basic-block) IRs vs **graph IRs** (like FX in PyTorch) — when each is used
  - [ ] High-level vs low-level IR (MLIR has both in one framework — [[../MLIR]])
  - [ ] Stack machines (JVM/CPython bytecode — see [[../../Programming/python/core/bytecode]]) vs register machines
- [x] **Optimization examples on SSA**:
  - [x] Dead code elimination (DCE)
  - [x] Common subexpression elimination (CSE)
  - [x] Copy propagation and why SSA makes it trivial
- [ ] **Static single information (SSI)** / other IR forms — optional, once the basics are solid.

## Prioritization

1. **Liveness** then **SSA construction** — the two missing pillars of SSA theory.
2. **Phi elimination + DCE/CSE** — small, concrete, and immediately useful.
3. **Register allocation** — after liveness makes sense.
