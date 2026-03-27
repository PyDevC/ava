# Dominators

A node 'A' is said to strictly dominate a different node 'B' if it is impossible to reach 'B' without going through 'A' first.
A is said to dominate B if A = B or A strictly dominates B.

## Algorithm to find the dominance frontier

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
