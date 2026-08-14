# Diffusion models

Diffusion models generate data by **reversing a corruption process**. They're currently the state of the art for image generation (Stable Diffusion, DALL·E, Imagen, Sora).

## The two processes

**Forward (corruption)**: take a real image `x₀`, add Gaussian noise over `T` steps until it's pure noise `x_T ~ N(0, I)`. Defined by a fixed schedule `β_t`:

```
x_t = √(1-β_t)·x_{t-1} + √β_t·ε
```

**Backward (denoising / generation)**: learn to remove the noise — a network `ε_θ(x_t, t)` predicts the noise added at step `t`. Generate by starting from random noise and denoising `T` times.

## Why it works

- The forward process has a closed form: `x_t = √ᾱ_t·x₀ + √(1-ᾱ_t)·ε` — so training never needs to iterate noise; just sample a random `t`, corrupt once, and predict the noise.
- Training objective: `L = E[‖ε - ε_θ(x_t, t)‖²]` — simple L2 regression against the noise, which is a [[../mathematical-function/normal-distribution]] regression. The U-Net (or now transformer/DiT) learns to denoise.

## The key ideas to know

- **Latent diffusion**: run the diffusion in a low-dim *latent* space (autoencoder-encoded, see [[embeddings]]/[[../algorithms/PCA]]-adjacent) instead of pixel space — that's what makes Stable Diffusion tractable.
- **Sampling**: fewer denoising steps via DDIM (deterministic reparam) or flow matching — speed vs quality trade-off.
- **Conditioning**: guide the denoiser with text (cross-attention to text embeddings), class labels, or images — this is where the transformer/attention machinery from [[Transformers]] enters.

## Why they beat GANs

- **Stable training**: a simple L2 regression objective, no adversarial equilibrium (see [[GANs]]).
- **Mode coverage**: diffusion models cover the whole data distribution instead of collapsing.
- Cost: sampling is 10–50 sequential denoise steps vs GANs' one shot.

## Related

- [[../mathematical-function/normal-distribution]] — the noise source.
- [[GANs]] — the adversarial predecessor.
- [[embeddings]] — the latent space conditioning runs through.
- [[Transformers]] — modern diffusion backbones are transformers (DiT).
