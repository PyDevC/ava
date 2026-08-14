# LLM / NLP engineer

The modern high-demand role: building products on large language models — RAG, agents, fine-tuning, and evaluation. Sits between the modeling world ([nlp](../MachineLearning/nlp/PLAN.md)) and production engineering.

## Core skills

- **LLM application patterns** — the modern NLP stack:
  - **RAG** ([RAG](../MachineLearning/nlp/RAG.md)): retrieval, chunking, reranking, grounding. The #1 production skill.
  - **Prompting + agents** ([prompting-in-context-learning](../MachineLearning/nlp/prompting-in-context-learning.md), [agents-tool-calling](../MachineLearning/nlp/agents-tool-calling.md)): tools, loops, structured output, safety/injection.
  - **Fine-tuning** ([nlp-tasks-fine-tuning](../MachineLearning/nlp/nlp-tasks-fine-tuning.md)): SFT, LoRA/adapters, RLHF/DPO ([instruction-tuning-rlhf](../MachineLearning/nlp/instruction-tuning-rlhf.md)).
  - **Evaluation** ([GPT-evaluation](../MachineLearning/nlp/GPT-evaluation.md)): benchmarks, LLM-as-judge, groundedness, cost/latency metrics.
- **Infrastructure**: serving LLMs (vLLM/TGI), KV cache, quantization for inference ([model-optimization-for-inference](../MachineLearning/deeplearning/model-optimization-for-inference.md)), embedding/vector stores ([embeddings-for-search](../MachineLearning/nlp/embeddings-for-search.md)).
- **The engineering base**: concurrency/async for the agent loop ([asyncio](../Programming/python/asyncio.md)), caching, observability (token usage, latency, cost per request).

## A typical day

Build/extend an LLM feature: design the retrieval pipeline, craft prompts, wire a tool call, measure quality on the eval set, watch cost/latency in prod. The work is *integration + measurement* more than model training — most days involve no training at all.

## The roadmap

1. **NLP fundamentals**: [tokenization](../MachineLearning/nlp/tokenization.md), [GPT-decoder-only-models](../MachineLearning/nlp/GPT-decoder-only-models.md), [BERT](../MachineLearning/nlp/BERT.md), [Transformers](../MachineLearning/deeplearning/Transformers.md).
2. **Applications**: RAG → prompting → agents → fine-tuning → eval. This is the job.
3. **Production**: serving, vector DBs, observability, cost management (tokens ≈ money).
4. **MLOps overlap**: monitoring drift, retraining, model registry (see [mlops-engineer](mlops-engineer.md)).

## How this repo maps

- [PLAN](../MachineLearning/nlp/PLAN.md) — the whole application stack is written out here.
- [Transformers](../MachineLearning/deeplearning/Transformers.md) — the architecture underneath.
- [Comparison-Matrix](../Compilers/DLCompilers/Comparison-Matrix.md) — the serving/compile choice.
- [asyncio](../Programming/python/asyncio.md) — the agent-loop concurrency.

## Related

- [ml-engineer](ml-engineer.md) — the broader ML umbrella.
- [ai-engineer](ai-engineer.md) — the pre-LLM version of "build on models".
- [mlops-engineer](mlops-engineer.md) — the deployment half.
- [ai-compiler-engineer](ai-compiler-engineer.md) — the performance-engineering extreme.
