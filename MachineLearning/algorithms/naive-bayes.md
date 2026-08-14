# Naive Bayes

Naive Bayes is a **generative** classifier based on Bayes' rule, with one brutally simplifying assumption: **features are conditionally independent given the class**.

```
P(y | x) ∝ P(y) · Π P(xᵢ | y)
```

The "naive" part is the independence assumption — it's almost never literally true, yet the classifier works surprisingly well in practice.

## Why it works despite being "wrong"

- The assumption rarely *hurts* the *ranking* of classes, even when it distorts the probabilities. Perfect probability calibration isn't needed for a good argmax.
- It's extremely data-efficient: each `P(xᵢ | y)` is a small table / single distribution, learned from few examples.
- Cheap, fast, handles high-dimensional discrete features (text!) well — it was the workhorse for spam filtering and early text classification.

## Variants (by feature type)

- **Bernoulli / Multinomial NB** — binary / count features (bag-of-words text).
- **Gaussian NB** — continuous features modeled as Gaussians per class.
- **Categorical NB** — categorical features.

## The generative view

Unlike discriminative models ([logistic-regression](logistic-regression.md), [SVM](SVM.md)) that learn `P(y|x)` directly, NB models the *joint* `P(x, y) = P(y)·ΠP(xᵢ|y)` and derives the posterior. This means it can also *sample* new examples of each class — the generator half of generative modeling.

## Practical notes

- Feature *log*-probability sums (use `log P`) — numerically stable and avoids underflow (see [logit-function](../mathematical-function/logit-function.md)).
- Laplace smoothing (`alpha`) handles zero-count categories — a small count added to every bin.
- Strong baseline for text; for tabular data, boosted trees usually win (see [tabular-regression](tabular-regression.md)).

## Related

- [classification](classification.md) — the task.
- [logistic-regression](logistic-regression.md) — the discriminative contrast (NB is generative).
- [loss-function](loss-function.md) — cross-entropy view: NB minimizes log loss on the joint model.
