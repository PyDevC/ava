---
id: softmax-function
aliases: []
tags: []
---

The softmax function is a function that turns a vector of K real values into a
vector of K real values that sum to 1. The output of the function is always 
between 0 and 1, which can be used as a probability score.

```
softmax(z)_i = exp(z_i) / Σ_j exp(z_j)
```

It "softens" the argmax: the largest input gets the largest probability, but all values get *some* weight (vs. a hard [[indicator-function]]/argmax, which gives 1 to the winner and 0 to everything else).

## Key properties

- Outputs form a probability distribution: `Σ_i softmax(z)_i = 1`, all in `(0,1)`.
- **Shift invariance**: `softmax(z) = softmax(z + c)` (adding a constant doesn't change it) — this is why implementations subtract the max `z` first for numerical stability.
- **Temperature**: `softmax(z/T)` — higher `T` smooths (more uniform), lower `T` sharpens (hotter). Used in LLM sampling.
- Differentiable — gradients flow to all inputs, scaled by the probabilities (the "soft" part).

## Where it's used

- **Multiclass classification**: the output layer of a classifier → probability over classes; trained with cross-entropy / log loss. See [[MachineLearning/algorithms/classification]].
- **Attention weights**: in Transformers, attention weights are softmax over score vectors — see [[MachineLearning/deeplearning/Transformers]] and the [[MachineLearning/deeplearning/attention-is-all-you-need]] paper notes.
- **Ranking losses** (listwise): softmax over scores, e.g. ListNet — see [[MachineLearning/algorithms/ranking]].
- The multi-class analog of the [[logistic-function]] (sigmoid = softmax on 2 classes).

## Related

- [[logistic-function]] — the binary case.
- [[logit-function]] — its inverse; log-softmax is the numerically-stable form.
- [[indicator-function]] — the "hard" version softmax is a relaxation of.
- [[MachineLearning/mathematical-function/softmax-function|softmax]]'s derivative: `J_i = s_i·(δ_ij - s_j)`.
