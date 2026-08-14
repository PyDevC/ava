# Injectivity

A function `f: A → B` is **injective** (one-to-one) if different inputs always map to different outputs:

```
f(a) = f(b)  ⟹  a = b
```

No two distinct elements of the domain share the same image. Equivalently: the graph of `f` never has a horizontal line crossing it twice.

## Why it matters in ML

- **Information preservation**: an injective map never collapses two different inputs, so nothing gets lost — this is what makes `map`/feature-encodings lossless.
- **Autoencoders / representation learning**: we *want* the encoder to be (close to) injective so the latent code uniquely identifies each input.
- **Universal approximation** (see [universal-approximation-function](universal-approximation-function.md)): with injective encoders + smooth decoders you can build networks that separate distinct points while reconstructing them.
- **Generative models**: a generative function should be "almost injective" — distinct latents should give distinct samples.
- **Dimensionality reduction** (e.g. SOM in the same folder) tries to embed data *approximately* injectively into a low-dim space.

## Non-injective = lossy

If `f` isn't injective, some information about the input is discarded (the pre-image becomes a set). This is sometimes intended (quantization, pooling) and sometimes the root of a bug.

## Related

- A function with an **inverse** is always injective.
- In linear algebra: a linear map is injective iff its kernel is trivial (`Ker(f) = {0}`), iff columns are linearly independent, iff rank = number of columns.
