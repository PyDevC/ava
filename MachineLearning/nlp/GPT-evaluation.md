# GPT evaluation

How do you measure an LLM? Task metrics (BLEU/ROUGE) were built for the pre-LLM era and are crude for modern models; the field has layered on benchmarks, human eval, and model-as-judge. This is the "measurement" chapter of the NLP plan.

## The classic task metrics (and why they're crude)

- **Perplexity**: `exp(−mean log P(token))` — how surprised the model is by held-out text. Useful for *training* monitoring, near-useless for *task* quality (low perplexity ≠ good answers).
- **BLEU** (translation): n-gram precision with brevity penalty. *Precision-based* → rewards matching exact reference n-grams; flaky for creative/paraphrased output.
- **ROUGE** (summarization): n-gram *recall* against references. Same fragility. Both reward surface overlap, not meaning — a paraphrase scores near zero while being a better answer.

## The LLM-era toolkit

- **Benchmarks** — fixed tasks with fixed answers:
  - Knowledge: MMLU, GPQA. Reasoning: GSM8K (math), HumanEval/MBPP (code), BBH. Instruction following: IFEval. Agentic: SWE-bench (real GitHub issues).
  - Every one is *contaminated* (appears in pretraining data) — benchmark scores overstate real capability.
- **LLM-as-judge**: use a strong model to score output quality (helpfulness, correctness, format) with a rubric. Scalable, correlates reasonably with humans — *if* you watch for judge bias (prefers longer/self-similar output).
- **Human eval**: the gold standard; slow and expensive → used on samples + as judge-training data (see [instruction-tuning-rlhf](instruction-tuning-rlhf.md)).
- **Task-specific / practical**: for RAG ([RAG](RAG.md)): retrieval recall@k, faithfulness (does the answer follow the docs), citation accuracy. For agents: task success rate, steps, cost.

## How to actually measure a system

1. **Define the user goal** first (answer grounded? follows the format? solves the ticket?).
2. Pick 1–2 proxy metrics per goal — *not* 10.
3. Build a small labeled **eval set** (50–200 examples), including edge cases (adversarial, injection, out-of-scope).
4. Regression-run it on every prompt/tool/model change (see [prompting-in-context-learning](prompting-in-context-learning.md)).
5. Track a **human-validated sample** — the proxies lie; spot-check.

## Related

- [model-interpretability](../algorithms/model-interpretability.md) — eval overlaps with "is the model doing what we think".
- [instruction-tuning-rlhf](instruction-tuning-rlhf.md) — the reward model is itself an evaluator.
- [RAG](RAG.md) — the concrete evaluation target.
