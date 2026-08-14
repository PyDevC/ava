# Reptile and meta-learning for RL

Reptile is the **first-order simplification** of MAML ([model-agnostic-meta-learning](model-agnostic-meta-learning.md)): same goal — learn initial weights that adapt fast — but a far simpler update that avoids the Hessian (second-order) terms MAML pays for.

## The algorithm

Repeat:
1. Sample a task; train from the current weights `φ` for `k` steps, reaching `φ'`.
2. Move `φ` toward `φ'`: `φ ← φ + ε(φ' − φ)`.

That's it — no differentiation through the inner loop, no gradient-of-gradient. The update is just **"move the init toward the post-adaptation weights"**.

## Why it works (the theory, in one line)

MAML does gradient descent on the *meta-objective* `minimize Σ L_task(adapt(φ))` (requires the Hessian). Reptile approximates that gradient with the **difference `φ' − φ`** — which is a first-order estimate of the same direction. For `k=1` with SGD it's provably related to MAML; empirically it matches or beats MAML on few-shot benchmarks at a fraction of the cost.

## Meta-learning for RL (the harder sibling)

- The same "learn to adapt fast" idea applies to RL: meta-RL learns a policy or an init such that a new task's reward structure is exploited within a few episodes. The reward signal replaces labels; episodes replace examples.
- Classic results: **RL²** (an RNN meta-learner that maps the whole episode history to actions — the policy *is* the learning algorithm), **MAML for RL** (init policy adapts to new dynamics), and the modern **in-context RL** (a transformer that conditions on past trajectory — see [prompting-in-context-learning](../nlp/prompting-in-context-learning.md)).

## The bridge to modern ML

- Reptile's "one update" view is the ancestor of today's **in-context learning** intuitions: adapt to a task by conditioning on a few examples rather than weight updates (see [prompting-in-context-learning](../nlp/prompting-in-context-learning.md)).
- Meta-learning's "training procedure that learns procedures" is literally what a foundation model's few-shot prompting does — see the in-context note in the automl [PLAN](../).

## Related

- [model-agnostic-meta-learning](model-agnostic-meta-learning.md) — the second-order original.
- [meta-learning](meta-learning.md) / [few-shot-learning](few-shot-learning.md) — the umbrella ideas.
- [prompting-in-context-learning](../nlp/prompting-in-context-learning.md) — the modern instantiation.
