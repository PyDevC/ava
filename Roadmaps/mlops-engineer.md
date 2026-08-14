# MLOps engineer

The **deployment half** of ML — the role that takes a trained model and makes it run reliably in production. Where [ml-engineer](ml-engineer.md) stops at "the model is accurate", MLOps is: serving, monitoring, retraining, CI/CD for models, and infrastructure.

## Core skills

- **Serving**: model servers (Triton Inference Server, TorchServe, FastAPI-based), batching, autoscaling, latency budgets. The deployment-story in [model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md) (quantization, compilation via [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md)) is the model-side; serving infra is the MLOps side.
- **Training infrastructure**: GPU clusters, job schedulers (Kubernetes + KubeFlow/Volcano, Slurm), data pipelines (Airflow/Prefect/Luigi), experiment tracking (MLflow, Weights & Biases) — see the reproducibility discipline in [tuning-methodology](../MachineLearning/automl/hyperparameter-tuning/tuning-methodology.md).
- **Model lifecycle**: registries (MLflow Model Registry, Hugging Face Hub), versioning, **shadow/canary/rollback** deploys, monitoring (drift detection on inputs and outputs — [model-interpretability](../MachineLearning/algorithms/model-interpretability.md) has the "is it still doing what I think" view).
- **CI/CD for ML**: automated retraining pipelines, data validation (Great Expectations), model revalidation before promotion. The same review discipline as [PR-Lifecycle](../OpenSource/PR-Lifecycle.md) but for models.

## A typical day

Mostly: infrastructure debugging (a training job OOM'd), serving performance (why is p99 latency up?), pipeline fixes (a data source broke), and model promotions (new version → shadow → full). The "glue" — Kubernetes YAML, Terraform, CI configs, monitoring dashboards — is the job description.

## The roadmap

1. **DevOps base**: Docker, Kubernetes, Terraform, CI (GitHub Actions), Linux — the general-SWE layer (see [hpc-engineer](hpc-engineer.md)/[devops-engineer](devops-engineer.md)).
2. **Training infra**: GPU scheduling, job orchestration, experiment tracking, data pipelines.
3. **Serving + monitoring**: model serving stacks, latency/throughput tuning, drift monitoring, retraining loops.
4. **Automation**: end-to-end pipelines, model registry, promotion/rollback.

## How this repo maps

- [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md) — the "which compiler/runtime" decision serving needs.
- [tuning-methodology](../MachineLearning/automl/hyperparameter-tuning/tuning-methodology.md) — eval discipline that carries into monitoring.
- [cpp-cmake](../Programming/cpp/cpp-cmake.md) / [packaging](../Programming/python/packaging.md) — the build/deploy tooling.
- [hpc-engineer](hpc-engineer.md) — the cluster side.

## Related

- [ml-engineer](ml-engineer.md) — the modeling counterpart.
- [ai-engineer](ai-engineer.md) — the application-building counterpart.
- [devops-engineer](devops-engineer.md) — the general-infra baseline.
- [GPT-evaluation](../MachineLearning/nlp/GPT-evaluation.md) — the monitoring metrics mindset.
