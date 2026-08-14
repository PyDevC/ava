# LightGBM

LightGBM (Light Gradient Boosting Machine) is a gradient boosting framework from Microsoft, designed to be faster and more memory-efficient than [XGBoost](XGBoost.md) while keeping (usually) comparable or better accuracy, especially on large datasets.

## Key ideas

- **GOSS — Gradient-based One-Side Sampling**: during boosting, samples with small gradients (already well-predicted) are randomly downsampled, while large-gradient samples are kept. This focuses training on the hard examples with much less data.
- **EFB — Exclusive Feature Bundling**: groups mutually-exclusive features (features that are rarely non-zero together, common in sparse data) into a single feature, reducing dimensionality without losing information.
- **Leaf-wise growth**: unlike XGBoost's level-wise growth, LightGBM splits the leaf with the highest loss reduction each time. Risk: overfitting on small data — control with `max_depth` / `num_leaves`.
- **Histogram-based split finding**: buckets continuous features into discrete bins once, then accumulates gradients per bin. Massive speedup vs sorting-based XGBoost.

## When to choose it vs XGBoost

- LightGBM: large datasets, sparse features, want speed/memory wins. Set `max_depth` low to avoid overfit on small data.
- XGBoost: solid defaults, good for moderate data, strong regularization story, and lots of framework integrations (sklearn API, distributed modes).

## Notes

- Categorical features supported natively (`categorical_feature`).
- `num_leaves` is the most important tuning knob; `min_data_in_leaf` helps avoid overfitting.
- Both are specializations of [gradient-boosting](gradient-boosting.md) with decision trees as the weak learner ([decision-trees](decision-trees.md)).
