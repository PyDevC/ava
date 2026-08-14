# Automated feature extraction

Feature extraction = transforming raw data into a **set of informative, model-ready features**. "Automated" means this step is done by algorithms instead of manual domain engineering.

## Manual vs automated

- Manual: domain knowledge → hand-crafted features (ratios, aggregates, text regexes...). Expensive, brittle, human.
- Automated: the algorithm discovers features from raw data. Two big families:

## Family 1 — Learned representations (end-to-end)

The model learns its own features from raw input:

- **Neural networks** — a CNN learns edge→texture→object features automatically; see [[MachineLearning/deeplearning/An-introduction-to-convolutional-neural-networks]]. This is the ultimate "automated feature extraction".
- **Autoencoders / representation learning** — unsupervised encoders that compress data into a latent space (see [[MachineLearning/algorithms/injectivity]] for why injective encoders preserve info).
- **Embedding layers** — learn dense vectors for categorical tokens (the [[MachineLearning/nlp/BERT]] story).

## Family 2 — Automated feature engineering tools

Algorithmic search over feature transforms for tabular data:

- **Featuretools (DFS)** — Deep Feature Synthesis: applies primitives (mean, count, time-since...) across entity relationships.
- **tpot / auto-sklearn pipelines** — feature selection/preprocessing operators are part of the searched pipeline (see [[tpot]]).
- **Embeddings of categories / t-SNE / PCA** — automatic *dimensionality-reducing* features.

## Why it matters

- Raw data is rarely directly learnable (text, images, tables with gaps).
- The best features usually beat the best model: garbage-in/garbage-out still holds.
- Modern trend: for unstructured data, end-to-end learning replaces manual feature extraction; for tabular data, automated FE + tree models is still the strongest combo.

## Related

- [[MachineLearning/automl/automated-feature-extraction|this]] is one stage of the AutoML pipeline — see [[auto-sklearn]].
- [[metric-learning]] is a special learned-feature technique (features that make similar pairs close).
