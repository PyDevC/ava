# ML Engineer

This job requires you to wear a lots of hats namely MLOps Engineer, ML Researcher, Data Engineer, Model Optimization Engineer(Not most of the time), etc.

This career demands a lots of experties depending on domain of your company.

## The core identity

At its heart the ML Engineer **productionizes models**: trains them, makes them fast and reliable, and ships them behind a service. Less "invent new algorithms" (that's [[research-scientist]]), less "pure product engineering" (that's [[ai-engineer]]), more "make ML work at scale, end to end".

## The hats you wear (from the intro)

- **Data Engineer-ish**: building/cleaning datasets, feature pipelines, versioning data.
- **MLOps**: experiment tracking, training pipelines, model registries, deployment, monitoring/drift.
- **Model Optimization (sometimes)**: quantization, pruning, distillation, `torch.compile` to hit latency budgets — see [[MachineLearning/deeplearning/model-optimization]] and [[Compilers/DLCompilers/TorchInductor/TorchInductor]].
- **SWE**: writing clean Python, tests, APIs around the model.
- **Light research**: when the company needs a bespoke architecture, you may need paper-level skills.

## Core skills checklist

- Python + software engineering habits; git, tests, Docker.
- Classic ML (sklearn, trees — [[MachineLearning/algorithms]]) **and** deep learning (PyTorch — [[PyTorch/Explore]]).
- Training infrastructure: GPUs, distributed training (DDP/FSDP), experiment tracking.
- Serving: REST/GRPC endpoints, batching, GPU inference optimization.
- Evaluation: proper metrics, holdout discipline, A/B testing.
- MLOps tools: MLflow/W&B, CI/CD, monitoring.

## Roadmap to get there

1. Python + data manipulation (pandas/numpy).
2. Classic ML course + sklearn projects (see [[MachineLearning/algorithms]]).
3. Deep learning in PyTorch (Transformers, CNNs).
4. ML production course: Docker, FastAPI serving, MLflow.
5. Optimization: `torch.compile`, quantization, profiling.
6. Keep shipping end-to-end projects — the portfolio is what gets hired.

## Related

- [[ai-engineer]] — nearly the same role, more LLM-product-skewed.
- [[data-analyst]] → [[ml-engineer]] is a common career ladder.
- [[devops-engineer]] — the infra half of MLOps.
- [[research-scientist]] — the research counterpart.

