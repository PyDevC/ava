# Prune SSA

> Minimal SSA is the SSA with fewest or almost fewest Φ() function possible, such that each unique name is assigned value only once, and in the original code the reference of that name can easily refer to that name.
> Some of the Φ functions are dead which means that they never receive the control from it's predecessor node.

Pruned SSA form is based on a simple observation: Φ functions are only needed for variables that are "live" after the Φ function.
