# Copy propagation

Copy propagation replaces a use of a variable with the value it was copied from. If `x = y` then all uses of `x` (before `x` is redefined) can read `y` instead — turning the copy into dead code that DCE then removes.

```
t = a
b = t * 2          →   b = a * 2         (t now dead, DCE removes it)
```

## Why SSA makes it trivial

In SSA form (see [[SsaConstruction]]), `x = y` means `x` is *defined once* and that def dominates all its uses. So the copy is simply rewritten: every use of `x` becomes a use of `y`, as long as `y` still dominates that point (it does — the copy def dominates its uses by construction, and `y` dominates the copy). There's no alias analysis, no reaching-definitions bookkeeping: it's a pure name substitution on the def-use chain.

This is the classic example of SSA making an optimization free: **copy propagation needs no dataflow analysis at all** in SSA form, because the def-use chains carry the answer.

## The interaction with coalescing

Copy propagation and register-allocation coalescing (see [[RegisterAllocation]]) are related but different:

- **Copy propagation** removes the copy by renaming uses.
- **Coalescing** keeps the copy but assigns `x` and `y` the same register so the copy is a no-op.

Propagation can blow up instruction sizes (a copy can be "useful" to keep because it truncates live ranges); coalescing exists precisely to remove copies *without* extending live ranges. Real compilers balance the two.

## Related

- [[DeadCodeElimination]] — removes the now-dead copies propagation creates.
- [[CommonSubexpressionElimination]] — the sibling "redundancy" pass.
- [[SsaConstruction]] — why the whole thing is cheap in SSA.
