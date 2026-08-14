# mathematical-function — Plan

## Goal of this directory

Catalog the **mathematical functions** that show up repeatedly in ML — activations, links, distributions, and structural functions — as a quick-reference "math toolbox". Each note: definition, shape/intuition, why it matters in ML, and links to where it's used.

## What's already here

- Activations/links: [logistic-function](logistic-function.md), [logit-function](logit-function.md), [softmax-function](softmax-function.md), [relu-function](relu-function.md), [tanh-function](tanh-function.md), [hard-activations-step](hard-activations-step.md)
- Distributions: [normal-distribution](normal-distribution.md), [bernoulli-categorical](bernoulli-categorical.md), [uniform-distribution](uniform-distribution.md)
- Structural: [affine-function](affine-function.md), [linear-function](linear-function.md), [indicator-function](indicator-function.md), [quantile-function](quantile-function.md), [lipsschitz-function](lipsschitz-function.md)
- Geometry/calculus: [norms](norms.md), [dot-product-cosine](dot-product-cosine.md), [gradient-hessian](gradient-hessian.md), [convexity-jensen](convexity-jensen.md), [exp-ln-logspace](exp-ln-logspace.md)

## What to add next

- [x] **The activation family** (the biggest gap — only logistic/softmax covered):
  - [x] ReLU and variants (LeakyReLU, ELU, SiLU/Swish, GELU)
  - [x] tanh (logistic's centered sibling)
  - [x] Hard sigmoid / hard tanh (quantized-friendly) — ties to inference optimization
- [x] **Distributions** (probabilistic view, extends [quantile-function](quantile-function.md)):
  - [x] Normal / Gaussian (CDF, z-scores, why the CLT matters)
  - [x] Bernoulli / Categorical (the classification outputs' distribution)
  - [x] Uniform (links to sampling — see quantile note's inverse-transform)
- [x] **Norms & geometry**:
  - [x] L1 / L2 / L-infinity norms (regularization, distance, lipschitz constant = operator norm)
  - [x] Dot product / cosine similarity (attention, embeddings, [metric-learning](../automl/metric-learning.md))
- [x] **Calculus tools**:
  - [x] Gradient and Hessian (links to [deeplearning](../deeplearning/PLAN.md)'s backprop plan)
  - [x] Jensen's inequality / convexity (why loss minimization is well-behaved)
- [x] **Misc useful**:
  - [x] exp/ln and why ML works in log space (numerical stability — see [logit-function](logit-function.md)'s note)
  - [x] Heaviside/step (the indicator's sibling)

## Prioritization

1. **ReLU family + tanh** — used in every network I train.
2. **Normal/Bernoulli/Categorical** — the distributions behind regression and classification losses.
3. **L1/L2 norms + gradient/Hessian** — needed by the optimizer and regularization notes.
