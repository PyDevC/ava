# PCA (Principal Component Analysis)

PCA is the classic **dimensionality reduction** technique: find the directions of maximum variance in the data and project onto them. It compresses `d` features into `k < d` uncorrelated components while keeping as much variance (information) as possible.

## What it computes

1. Center the data (subtract the mean of each feature).
2. Compute the covariance matrix (or do SVD directly on the data matrix).
3. The **principal components** are the top-`k` eigenvectors of the covariance (equivalently, the top-`k` right-singular vectors of the centered data).
4. Each component is a *direction*; the projected coordinates are the new features, and they're **uncorrelated** by construction.

The eigenvalue (singular value) tells you how much variance that component captures — so you can measure "I kept 95% of the variance with 10 components."

## Why it matters

- **Visualization**: project to 2–3 dims to plot high-dim data (like [[SOM]] does, but linear).
- **Noise reduction / compression**: drop the low-variance directions.
- **Speed**: train on fewer, decorrelated features (helps linear models; see [[tabular-regression]]).
- **Data whitening / decorrelation**: a preprocessing step for some algorithms.

## The caveats

- **Linear only** — PCA finds a hyperplane. Non-linear structure needs t-SNE/UMAP/autoencoders.
- **Variance ≠ importance** — a low-variance direction can still be the one that separates classes. PCA is unsupervised: it never looks at labels.
- **Scale-sensitive** — on raw features, high-variance features dominate. Standardize first (or use correlation matrix) unless scale is meaningful.

## The information view

PCA is a *lossy* compression that preserves the most variance: in the [[injectivity]] framing it deliberately gives up exact invertibility to squeeze out noise — an **approximately injective** map onto a subspace. Autoencoders (neural nets) learn the same idea non-linearly; see [[automated-feature-extraction]].

## Related

- [[automated-feature-extraction]] — PCA is an automated, unsupervised feature transform.
- [[k-means]] — the two most-used unsupervised tools.
- [[SOM]] — the non-linear neighbor for visualization.
- [[metric-learning]] — PCA finds variance; metric learning finds *task-relevant* directions.
