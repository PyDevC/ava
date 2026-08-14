# Bernoulli and Categorical distributions

These are the two discrete distributions behind classification — they're the *generative* view of what classifiers output.

## Bernoulli (binary outcome)

`X ∈ {0, 1}` with `P(X=1) = p`:

- Mean = p, variance = p(1-p).
- A single coin flip, a single yes/no label, a mask entry.
- This is the distribution behind **binary classification**: the model predicts `p` (see [logistic-regression](../algorithms/logistic-regression.md)).

## Categorical (K outcomes)

`X ∈ {1..K}` with probabilities `p₁..p_K`, Σp = 1:

- The generalization of Bernoulli to K classes; a single roll of a K-sided die.
- This is what **multiclass classification** models with a [softmax-function](softmax-function.md) output: the model predicts the whole vector `p`.
- A **multinomial** distribution is `n` categorical draws (counts per class) — used by Naive Bayes for text (see [naive-bayes](../algorithms/naive-bayes.md)).

## Why they matter

- **The loss matches the distribution**: cross-entropy loss is the negative log-likelihood of the Bernoulli/Categorical model (see [loss-function](../algorithms/loss-function.md)). Training = maximum likelihood estimation of `p`.
- **Sampling**: generating discrete outputs (sampling a token from an LLM) draws from a Categorical over the vocabulary.
- **Gumbel-softmax / straight-through**: to backprop through discrete sampling, people relax the Categorical to a softmax with temperature — see [softmax-function](softmax-function.md)'s temperature note.

## Related

- [softmax-function](softmax-function.md) — the parameterization of the Categorical.
- [logistic-function](logistic-function.md) — the parameterization of the Bernoulli.
- [classification](../algorithms/classification.md) — where both show up.
