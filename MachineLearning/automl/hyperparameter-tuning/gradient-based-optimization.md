# Gradient-based hyperparameter optimization

Gradient-based optimization tunes hyperparameters by **computing gradients of the validation loss w.r.t. the hyperparameters** and following them downhill — instead of treating the objective as a black box ([[hyperparameter-tuning/Bayesian-optimization]], [[random-search]]).

## How it works

1. Choose *differentiable* hyperparameters: learning rate `η`, weight decay `λ`, sometimes architecture knobs (via differentiable architecture search / DARTS).
2. Unroll the training: `θ_T` is a function of `θ₀` and the hyperparams through the gradient steps.
3. Compute `∂L_val(θ_T)/∂h` by **backprop through the optimization** (the "hypergradient").
4. Update the hyperparameters with a gradient step on the validation loss.

In the simplest 1-step case with SGD: `θ₁ = θ₀ - η·∇L_train(θ₀)`, then `∂L_val(θ₁)/∂η` is cheap and closed-form.

## Pros & cons

- **+** Scales to many continuous hyperparameters and to huge models — where BO and Hyperband become infeasible (deep learning).
- **−** Needs the training to be differentiable end-to-end (memory-heavy: must store/backprop through the unrolled steps; approximations like "implicit diff" or short unrolling exist).
- **−** Discrete hyperparameters (layer counts, choices) don't fit — need relaxations.

## Related

- [[MachineLearning/deeplearning/how-to-use-lr-scheduler]] — learning-rate schedules are exactly the kind of thing this tunes.
- [[hyperparameter-tuning]] — the umbrella.
- Contrast with [[Hyperband]] (bandit, no gradients) and [[hyperparameter-tuning/Bayesian-optimization]] (surrogate, no gradients).
