# MLOps engineer

The **deployment half** of ML — the role that takes a trained model and makes it run reliably in production. Where [[ml-engineer]] stops at "the model is accurate", MLOps is: serving, monitoring, retraining, CI/CD for models, and infrastructure.

## Core skills

- **Serving**: model servers (Triton Inference Server, TorchServe, FastAPI-based), batching, autoscaling, latency budgets. The deployment-story in [[MachineLearning/deeplearning/model-optimization-for-inference]] (quantization, compilation via [[Compilers/DLCompilers/Comparison-Matrix]]) is the model-side; serving infra is the MLOps side.
- **Training infrastructure**: GPU clusters, job schedulers (Kubernetes + KubeFlow/Volcano, Slurm), data pipelines (Airflow/Prefect/Luigi), experiment tracking (MLflow, Weights & Biases) — see the reproducibility discipline in [[MachineLearning/automl/hyperparameter-tuning/tuning-methodology]].
- **Model lifecycle**: registries (MLflow Model Registry, Hugging Face Hub), versioning, **shadow/canary/rollback** deploys, monitoring (drift detection on inputs and outputs — [[MachineLearning/algorithms/model-interpretability]] has the "is it still doing what I think" view).
- **CI/CD for ML**: automated retraining pipelines, data validation (Great Expectations), model revalidation before promotion. The same review discipline as [[OpenSource/PR-Lifecycle]] but for models.

## A typical day

Mostly: infrastructure debugging (a training job OOM'd), serving performance (why is p99 latency up?), pipeline fixes (a data source broke), and model promotions (new version → shadow → full). The "glue" — Kubernetes YAML, Terraform, CI configs, monitoring dashboards — is the job description.

## The roadmap

1. **DevOps base**: Docker, Kubernetes, Terraform, CI (GitHub Actions), Linux — the general-SWE layer (see [[hpc-engineer]]/[[devops-engineer]]).
2. **Training infra**: GPU scheduling, job orchestration, experiment tracking, data pipelines.
3. **Serving + monitoring**: model serving stacks, latency/throughput tuning, drift monitoring, retraining loops.
4. **Automation**: end-to-end pipelines, model registry, promotion/rollback.

## How this repo maps

- [[Compilers/DLCompilers/Comparison-Matrix]] — the "which compiler/runtime" decision serving needs.
- [[MachineLearning/automl/hyperparameter-tuning/tuning-methodology]] — eval discipline that carries into monitoring.
- [[Programming/cpp/cpp-cmake]] / [[Programming/python/packaging]] — the build/deploy tooling.
- [[hpc-engineer]] — the cluster side.

## Related

- [[ml-engineer]] — the modeling counterpart.
- [[ai-engineer]] — the application-building counterpart.
- [[devops-engineer]] — the general-infra baseline.
- [[../MachineLearning/nlp/GPT-evaluation]] — the monitoring metrics mindset.
