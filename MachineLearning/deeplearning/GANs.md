# GANs

Generative Adversarial Networks (Goodfellow et al. 2014) train a generator and a discriminator **against each other**:

- **Generator G**: maps noise `z ~ U(0,1)` (see [uniform-distribution](../mathematical-function/uniform-distribution.md)) into fake samples that try to fool the discriminator.
- **Discriminator D**: a binary classifier trying to tell real from fake.

The game: `D` gets better at detecting fakes; `G` gets better at faking. The equilibrium is a generator whose samples are indistinguishable from real data.

## The training loop

```python
# D step: maximize log D(real) + log(1 - D(fake))
# G step: minimize log(1 - D(G(z)))   →   maximize log D(G(z))
```

Each step updates one side while the other is frozen. Both are trained with the binary cross-entropy loss (see [loss-function](../algorithms/loss-function.md)).

## Why training is hard

- **Nash equilibrium, not minimization**: gradient descent on two adversarial objectives doesn't converge the way normal training does. Classic failure modes:
  - **Mode collapse**: `G` finds one sample that fools `D` and outputs only that.
  - **Oscillation / non-convergence**: the two players chase each other forever.
  - **Vanishing discriminator gradient**: when `D` is too good, `G` learns nothing.
- Practical mitigations: **Wasserstein loss** (WGAN — use earth-mover distance via a critic with Lipschitz constraint, see [lipschitz-function](../mathematical-function/lipschitz-function.md)), **spectral normalization**, **label smoothing**, feature matching.

## Where they stand now

For images, **diffusion models** (DDPM — denoise from noise step by step) overtook GANs on sample quality and stability. GANs still win on:
- Fast sampling (one forward pass vs ~50 diffusion steps).
- Some super-resolution / style transfer (pix2pix, CycleGAN, StyleGAN).

## Related

- [logistic-function](../mathematical-function/logistic-function.md) — the binary loss both players use.
- [lipschitz-function](../mathematical-function/lipschitz-function.md) — WGAN's constraint.
- [model-optimization-for-inference](model-optimization-for-inference.md) — a trained generator is a small feed-forward net (cheap to serve).
- [metric-learning](../automl/metric-learning.md) — feature matching / contrastive tricks appear in GAN discriminators.
