# Azure Machine Learning (AzureML)

AzureML is Microsoft's cloud platform for the full ML lifecycle: experiment tracking, AutoML, training at scale, model registry, and deployment. The parts I care about:

## The AutoML component

AzureML AutoML automates **model selection + hyperparameter tuning** for both tabular and (in the same product family) vision/NLP tasks:

- Searches over algorithms × preprocessing × hyperparameters using Bayesian optimization / random search / bandit-based early termination.
- Produces a leaderboard of models with metrics, explainability (SHAP) built in.
- Handles featurization: imputation, categorical encoding, scaling automatically.

## Core platform pieces

- **Workspace** — the container for everything (data, compute, runs, models).
- **Compute targets** — CPU/GPU clusters, or serverless compute.
- **Pipelines** — DAGs of data-prep + training + scoring steps.
- **Model registry** — versioned, deployable models.
- **Endpoint deployment** — managed online endpoints for real-time inference (or batch endpoints).

## The current story

Modern AzureML favors **MLflow** for tracking + **AzureML SDK v2 (Python)** with YAML specs. The "v1 vs v2" transition was painful but v2 (2022+) is the stable API now.

## Practical notes

- Best used when you already live in the Azure cloud ecosystem (integration with AKS, Storage, Key Vault).
- AutoML here is comparable to [[auto-sklearn]] / [[h2o-automl]], but fully managed and with cloud-scale compute.
- Costs money per compute-hour; set explicit limits.

## Related

- [[h2o-automl]], [[mlbox]], [[tpot]] — open-source/on-prem AutoML alternatives.
- [[hyperparameter-tuning]] — the underlying technique AutoML relies on.
