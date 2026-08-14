# Dominators

A node 'A' is said to strictly dominate a different node 'B' if it is impossible to reach 'B' without going through 'A' first.
A is said to dominate B if A = B or A strictly dominates B.

In other words, every path from the entry node to B must pass through A.

## Immediate dominator

The **immediate dominator** `idom(b)` of a node `b` is the unique node that strictly dominates `b` but does not strictly dominate any other node that strictly dominates `b`. It is the closest dominator to `b` along any path from the entry. Every node except the entry has exactly one immediate dominator, so `idom` forms a tree (the dominator tree) rooted at the entry node.

## Algorithm to find the dominance frontier

The classic algorithm is from Cooper, Harvey and Kennedy (2001). The dominance frontier of a node is the set of nodes where that node's dominance ends — i.e. the nodes whose other predecessors are not dominated by it. This is exactly what you need to know *where to insert Φ functions* when converting to SSA form.

```
for each node b
    dominance_frontier(b) := {}
for each node b
    if the number of immediate predecessors of b ≥ 2
        for each p in immediate predecessors of b
            runner := p
            while runner ≠ idom(b)
                dominance_frontier(runner) := dominance_frontier(runner) ∪ { b }
                runner := idom(runner)
```

In the code above, idom(b) is the immediate dominator of b, the unique node that strictly dominates b but does not strictly dominate any other node that strictly dominates b.

The idea: a join node `b` only needs a Φ function for a variable if the variable's definition dominates `b`'s predecessors but not `b` itself. The `while` loop climbs the dominator tree from each predecessor `p` up to (but not including) `idom(b)`, adding `b` to each node's frontier. Nodes on that climb dominate some predecessor of `b` but not `b`, which is precisely the frontier condition.

## Why bother

- Inserting Φ functions only at dominance frontiers keeps SSA **minimal** (fewest Φ's while still making each name unique).
- The dominance frontier is what makes the later algorithms (e.g. inserting Φ's for pruned SSA, see [PruneSSA](PruneSSA.md)) possible.
