# Phi elimination

Φ functions are an *IR-level* construct — real hardware has no "merge two incoming values" instruction. Before register allocation / codegen, every Φ must be lowered to **parallel copies** at the predecessors, then those copies resolved into concrete move instructions.

## Why it's non-trivial

A Φ at the join reads *different* variables per incoming edge. So for each edge `p → join`, at the end of `p` we copy `p`'s version into the join's name:

```
B1:                       B2:
  x1 = ...                  x2 = ...
  x2 := x1     # edge B1    x2 := x2     # edge B2 (self-copy, dropped)
        \
          x3 = Φ(x1, x2)   # join
```

## The swap problem

If we naively emit `a := b; b := a` for a parallel copy `(a,b) := (b,a)`, we clobber `b` before reading it. Parallel copies must be **atomic** — either:

1. **Temporary registers** — spill one value aside: `t := b; a := t; b := a`. Correct but wasteful.
2. **Cycle resolution** — decompose the permutation into cycles; for each cycle, use one temp to break it. This is the standard approach (Briggs et al. 1992) and is what LLVM does in `PHIElimination`.
3. **Coalescing** — if `a` and `b` end up in the same register, the copy disappears entirely (that's what coalescing passes hunt for).

## Ordering rules

- Copies must be emitted at the *end of each predecessor block* (before the branch), not in the join.
- All copies for one edge must be executed atomically (same temp for a whole cycle).
- Self-copies (`x := x`) are dead and removed.
- If a block has multiple Φ's, they form one parallel copy *set*; overlapping sets get merged so no value is clobbered between uses.

## Why it matters

Bad phi elimination = wrong code (values read after being overwritten) or redundant moves that kill register-allocation quality. It's one of those passes that looks trivial and isn't — the swap case is the classic interview trap.

## Related

- [SSAConstruction](SSAConstruction.md) — phis are created here, eliminated here.
- [RegisterAllocation](RegisterAllocation.md) — runs right after; coalescing with phi elimination is a major peephole win.
