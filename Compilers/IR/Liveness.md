# Liveness analysis

Liveness answers: **for each program point, which variables might still be used before being overwritten?** A variable is *live* at a point if its value could be read along some path from that point to the end of the program.

This is the analysis [[PruneSSA]] needs (dead phis are phis for dead variables), and the foundation of register allocation.

## The model

Each basic block has a set of *uses* (reads) and *defs* (writes):

- **`use[n]`** — variables read in block `n` before being written there.
- **`def[n]`** — variables written in block `n` (kills any incoming value).

A variable is live into a block if it's used before def'd in the block, or live-out and not killed. Formally (backward dataflow over the CFG):

```
live-out[n]  = ∪ live-in[s]        for all successors s of n
live-in[n]   = use[n] ∪ (live-out[n] − def[n])
```

Compute by iterating from the end of the CFG until no set changes (worklist algorithm). Initialize `live-out[exit] = ∅`.

## Why backward?

The property is about the *future*: a value is live now if it will be used later. So information flows against the direction of execution — backwards through the CFG, from later uses to earlier points. (Forward analyses like reaching-definitions flow the other way.)

## The dataflow equations, read carefully

- **`live-in[n] = use[n] ∪ (live-out[n] − def[n])`**: everything used in the block is live on entry; things live on exit stay live *unless* the block overwrites them first.
- **`live-out[n] = ∪ live-in[succ]`**: a value must be live at the end of a block if it's live at the start of any successor.

The union over *all* successors (not intersection) is important: a value is live if it's needed on *any* path.

## Why it matters

- **Register allocation** — only live variables need registers (see [[RegisterAllocation]]).
- **Pruned SSA** — only insert a phi for a variable live at the join (see [[PruneSSA]]).
- **Dead code elimination** — an instruction defining a variable that is never live is dead (see [[DeadCodeElimination]]).

## Related

- [[Dominators]] — the other workhorse analysis; both are required reading before SSA construction.
- [[SsaConstruction]] — puts liveness + dominance to work.
