# Self-organizing maps

Self-organizing maps (SOM, Kohonen maps) is an unsupervised technique that maps a high-dimensional dataset onto a low-dimensional grid (typically 2D) using value similarity, clustering similar points into fixed hexagonal/circular cells — all while preserving the data's topology. The result is a 2D visualization you can look at.

## How it learns

Instead of gradient descent + backpropagation, SOM uses **competitive learning**: nodes compete to respond to each input, and the winner (plus its neighbors) moves toward the input.

- Each node in the grid carries a weight vector in the input space.
- For each input, the **best matching unit** (BMU) — the node whose weights are closest — wins.
- The BMU's weights, and those of its grid neighbors, are pulled toward the input. The neighborhood shrinks over training, so the map first organizes globally, then refines locally.
- Because updates move *neighbors* too, similar inputs map to nearby cells — that's the topology preservation.

Two phases: **training** (the grid adjusts to the data) and **mapping** (new inputs are assigned to their nearest node, i.e. classified).

## Why it matters

- Dimensionality reduction + visualization: see high-dimensional clusters on a plane (vs [PCA](PCA.md), which is linear).
- After training, the map acts as a nearest-prototype classifier for new observations.

## Related

- [PCA](PCA.md) — the linear dimensionality-reduction alternative.
- [k-means](k-means.md) — the hard-clustering cousin (the grid adds topology).
- [mlab.taik.fi/~timo/som/thesis-som.html](https://mlab.taik.fi/~timo/som/thesis-som.html) — the original SOM thesis (Kohonen).
