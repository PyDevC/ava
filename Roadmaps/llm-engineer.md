# LLM / NLP engineer

The modern high-demand role: building products on large language models — RAG, agents, fine-tuning, and evaluation. Sits between the modeling world ([[MachineLearning/nlp]]) and production engineering.

## Core skills

- **LLM application patterns** — the modern NLP stack:
  - **RAG** ([[MachineLearning/nlp/RAG]]): retrieval, chunking, reranking, grounding. The #1 production skill.
  - **Prompting + agents** ([[MachineLearning/nlp/prompting-in-context-learning]], [[MachineLearning/nlp/agents-tool-calling]]): tools, loops, structured output, safety/injection.
  - **Fine-tuning** ([[MachineLearning/nlp/nlp-tasks-fine-tuning]]): SFT, LoRA/adapters, RLHF/DPO ([[MachineLearning/nlp/instruction-tuning-rlhf]]).
  - **Evaluation** ([[MachineLearning/nlp/GPT-evaluation]]): benchmarks, LLM-as-judge, groundedness, cost/latency metrics.
- **Infrastructure**: serving LLMs (vLLM/TGI), KV cache, quantization for inference ([[MachineLearning/deeplearning/model-optimization-for-inference]]), embedding/vector stores ([[MachineLearning/nlp/embeddings-for-search]]).
- **The engineering base**: concurrency/async for the agent loop ([[Programming/python/asyncio]]), caching, observability (token usage, latency, cost per request).

## A typical day

Build/extend an LLM feature: design the retrieval pipeline, craft prompts, wire a tool call, measure quality on the eval set, watch cost/latency in prod. The work is *integration + measurement* more than model training — most days involve no training at all.

## The roadmap

1. **NLP fundamentals**: [[MachineLearning/nlp/tokenization]], [[MachineLearning/nlp/GPT-decoder-only-models]], [[MachineLearning/nlp/BERT]], [[MachineLearning/deeplearning/Transformers]].
2. **Applications**: RAG → prompting → agents → fine-tuning → eval. This is the job.
3. **Production**: serving, vector DBs, observability, cost management (tokens ≈ money).
4. **MLOps overlap**: monitoring drift, retraining, model registry (see [[mlops-engineer]]).

## How this repo maps

- [[MachineLearning/nlp/PLAN]] — the whole application stack is written out here.
- [[MachineLearning/deeplearning/Transformers]] — the architecture underneath.
- [[Compilers/DLCompilers/Comparison-Matrix]] — the serving/compile choice.
- [[Programming/python/asyncio]] — the agent-loop concurrency.

## Related

- [[ml-engineer]] — the broader ML umbrella.
- [[ai-engineer]] — the pre-LLM version of "build on models".
- [[mlops-engineer]] — the deployment half.
- [[ai-compiler-engineer]] — the performance-engineering extreme.
