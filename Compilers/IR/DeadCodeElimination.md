# Dead code elimination (DCE)

DCE removes instructions that can never affect the program's observable behavior. It's the simplest optimization and the one that makes every other pass's cleanup trivial.

## The core rule

An instruction is **dead** if:
1. its result is never used, **and**
2. it has no side effects (no memory write, no store, no volatile read, no `call` that could observe anything).

The tricky part is #1 — "never used" must be computed, which is where liveness/def-use chains come in.

## Two classic formulations

**Local / iterative DCE (on def-use chains):**
```
worklist = all instructions
for each instruction with no uses and no side effects:
    remove it; for each operand, decrement use count;
    if that operand's use count hits 0 and it's side-effect-free, add to worklist
```

**Liveness-based DCE (on the CFG):** an instruction is dead if the variable it defines is never live-out (see [[Liveness]]) — equivalently, never used on any path after the def. This catches more cases than the use-count version because it reasons about *paths*, not just immediate uses.

## Why SSA makes DCE cleaner

In SSA form, every use points directly to its def (the unique reaching def). So "is this value used?" is a question about the def-use list — no must-alias or reaching-definitions analysis needed. This is one of the big practical wins of SSA (see [[SsaConstruction]]).

## The side-effect rule is what actually matters

`x = a + b` with no uses is dead. `y = store(x)` is **not**, even if `y` is unused — the store writes memory. Passes must conservatively keep anything that can be observed. This is also why "dead code elimination" in real compilers is usually *aggressive* DCE (ADC): treat anything not proven side-effect-free as live.

## Related

- [[Liveness]] — the analysis that powers the CFG form.
- [[CommonSubexpressionElimination]] — DCE's sibling; CSE creates redundancies that DCE then cleans up.
- [[PruneSSA]] — DCE is what makes pruning unnecessary for already-dead code; pruning prevents the dead phis in the first place.
