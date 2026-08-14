# mathematical-function — Plan

## Goal of this directory

Catalog the **mathematical functions** that show up repeatedly in ML — activations, links, distributions, and structural functions — as a quick-reference "math toolbox". Each note: definition, shape/intuition, why it matters in ML, and links to where it's used.

## What's already here

- Activations/links: [[logistic-function]], [[logit-function]], [[softmax-function]]
- Structural: [[affine-function]], [[linear-function]], [[indicator-function]], [[quantile-function]], [[lipsschitz-function]]

## What to add next

- [ ] **The activation family** (the biggest gap — only logistic/softmax covered):
  - [ ] ReLU and variants (LeakyReLU, ELU, SiLU/Swish, GELU)
  - [ ] tanh (logistic's centered sibling)
  - [ ] Hard sigmoid / hard tanh (quantized-friendly) — ties to inference optimization
- [ ] **Distributions** (probabilistic view, extends [[quantile-function]]):
  - [ ] Normal / Gaussian (CDF, z-scores, why the CLT matters)
  - [ ] Bernoulli / Categorical (the classification outputs' distribution)
  - [ ] Uniform (links to sampling — see quantile note's inverse-transform)
- [ ] **Norms & geometry**:
  - [ ] L1 / L2 / L-infinity norms (regularization, distance, lipschitz constant = operator norm)
  - [ ] Dot product / cosine similarity (attention, embeddings, [[../automl/metric-learning]])
- [ ] **Calculus tools**:
  - [ ] Gradient and Hessian (links to [[../deeplearning]]'s backprop plan)
  - [ ] Jensen's inequality / convexity (why loss minimization is well-behaved)
- [ ] **Misc useful**:
  - [ ] exp/ln and why ML works in log space (numerical stability — see [[logit-function]]'s note)
  - [ ] Heaviside/step (the indicator's sibling)

## Prioritization

1. **ReLU family + tanh** — used in every network I train.
2. **Normal/Bernoulli/Categorical** — the distributions behind regression and classification losses.
3. **L1/L2 norms + gradient/Hessian** — needed by the optimizer and regularization notes.
