# MLOps overview

MLOps is the practice of **running ML models reliably in production**: tracking experiments, managing model versions, serving predictions, and watching for the model going stale. The roadmap view is in [mlops-engineer](../../Roadmaps/mlops-engineer.md); this note is the concepts.

## Experiment tracking

Every training run needs a record: hyperparameters, dataset version, code version, and metrics across epochs. Tools (MLflow, Weights & Biases, neptune) store these centrally so runs are comparable.

- Log *everything* you'd need to reproduce the run — config, seed, data hash, commit SHA, not just the loss curve.
- Compare runs by the same [evaluation-metrics](../evaluation/evaluation-metrics.md) used for the final judgment, on the same splits.

## Model registry

A registry is a versioned store of **models, not runs** — each entry has artifacts (weights), metadata (metrics on a fixed eval set), and a stage (staging → production). Promotion is a deliberate, reviewed act (shadow → canary → full rollout), not a file copy. The registry is the boundary between "trained something" and "deployed something".

## Serving

- **Online (real-time)**: one request → one prediction, low latency. FastAPI wrapping a model, or a dedicated inference server (NVIDIA Triton, TorchServe) that also does batching and model multiplexing.
- **Batch (offline)**: precompute predictions over a large dataset on a schedule (overnight scoring, recommendation dumps). Simpler, no latency constraints, can use big batch sizes.
- The serving bottleneck is the model + its runtime, so the [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md) techniques (quantization, compilation) are the model-side half of the story.

## Monitoring and drift

After deployment, the model's world changes:

- **Data drift** — the input distribution shifts (new users, seasonal effects). Detect by comparing incoming feature distributions to training.
- **Concept drift** — the *relationship* between inputs and labels changes (a previously-good feature stops predicting). Detect via delayed labels / proxy metrics (e.g. engagement or downstream business metrics).
- **Model staleness** — the model just gets old: retraining cadence, or alert when drift metrics cross thresholds.
- Monitor both the inputs and the predictions' distribution, not just the serving health.

## Reproducibility

- **Seeds**: pin RNG seeds (numpy/torch/python) per run so results are rerunnable.
- **Data versioning**: hash the dataset (or use DVC-style tools) so a run's inputs are known.
- **Environment pinning**: record Python/CUDA/library versions — a requirements lockfile or container image per training run.

## CI/CD for models

- **CI**: on every PR — lint, unit tests, *train a smoke model on a data slice*, run a validation script (data schema checks, metric thresholds).
- **CD**: automated retraining pipelines and a promotion path through the registry with the same review discipline as [PR-Lifecycle](../../OpenSource/PR-Lifecycle.md), but the artifact is a model.

## Related

- [mlops-engineer](../../Roadmaps/mlops-engineer.md) — the role and roadmap this maps to.
- [model-optimization-for-inference](../deeplearning/model-optimization-for-inference.md) — the model-side speed/size work serving builds on.
- [evaluation-metrics](../evaluation/evaluation-metrics.md) — the scores tracking and monitoring compare against.
