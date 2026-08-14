# Neural Architecture Search (NAS)

NAS is the automl-of-NNs: instead of tuning hyperparameters for a fixed architecture, **search the architecture itself** (layers, widths, connections, ops). The evolution: RL-based search → weight-sharing/differentiable → zero-cost proxies.

## RL-based search (the original, 2017)

- A controller RNN (policy) emits a sequence describing the architecture (layer type, filter counts, strides); the child network trains; its validation accuracy is the **reward**; the controller updates via RL (REINFORCE) to propose better architectures next time.
- Zoph & Le showed it could beat hand-designed nets — but each child is trained from scratch → **thousands of GPU-days**. Impractical; motivated everything since.

## Weight sharing / one-shot / DARTS (the scalable turn)

- **Weight sharing**: instead of training each candidate from scratch, maintain one **super-network** whose weights are *shared* by all candidates. Search = finding the best sub-network.
  - **DARTS (Differentiable Architecture Search)**: make the choice *continuous* — a softmax over ops per edge; the search learns the mixing weights by **gradient descent** (hence "differentiable"); at the end, pick the argmax op. Cheap (a few GPU-days) but famously **unstable** (the continuous relaxation ≠ the final discrete net).
  - **One-shot/SPOS**: train the supernet once (uniformly), then evaluate candidates with the shared weights (no retraining per candidate) — the practical workhorse (EfficientNet-EfficientNetV2 lineage).
- The key result of this era: **searching in a "cell"-space** (small motif repeated) + weight sharing made NAS affordable; the found architectures (NASNet, EfficientNet) beat humans.

## Zero-cost proxies and pruning

- Even weight-shared search is expensive (train the supernet). **Zero-cost proxies** score candidates by *one forward/backward pass* on random data without any training — e.g. gradient-norm-based scores ("synflow", "NASWOT"), pruning-based measures (the magnitude-pruning view of [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md): a network's *prunability* correlates with quality).
- Cheap enough to search huge spaces; less reliable, but the modern default for "sanity check a design space".
- **Network pruning as search**: retraining + structured pruning + retraining is literally "search for the smallest good net" — see the inference-optimization note.

## The modern framing

- **LLM/transformer era**: NAS faded as *scale* + data beat architecture search for foundation models, but the ideas live on in: architecture *scaling laws* (EfficientNet-style width/depth resolution curves), "search-once-serve-anywhere" for edge, and **AutoML for deployment** (latency-constrained NAS, e.g. FBNet, ChamNet with the latency-pareto view from [tuning-methodology](hyperparameter-tuning/tuning-methodology.md)).
- Connections: DARTS/weight-sharing link to [backpropagation](../deeplearning/backpropagation.md) and [meta-learning](meta-learning.md) (learning the learning machinery).

## Related

- [PLAN](PLAN.md) — where NAS sits in the AutoML family.
- [meta-learning](meta-learning.md) — the learning-to-learn research side.
- [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md) — pruning, the NAS-adjacent technique.
- [tuning-methodology](hyperparameter-tuning/tuning-methodology.md) — the practical alternative for most projects.
