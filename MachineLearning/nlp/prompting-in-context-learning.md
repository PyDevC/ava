# Prompting and in-context learning

Prompting is the cheapest form of "programming" an LLM: no weight updates, no fine-tuning — just craft the input. It works because pretraining taught the model to continue text patterns, and the **context window** acts as a working set.

## The core techniques

- **Zero-shot**: "Classify this review: ..." → works surprisingly well; the model has seen instructions in pretraining data.
- **Few-shot / in-context learning**: give 2–5 *input → output* examples in the prompt, then the task. The model pattern-matches the examples. **The examples don't get trained on** — the performance gain is emergent from attention (this is "ICL", a big open research area).
- **Chain-of-thought (CoT)**: "Let's think step by step" (or give worked reasoning examples) → the model emits reasoning, and accuracy on math/logic jumps. The reasoning tokens effectively give it scratch space.
- **Structured output**: "Reply ONLY in JSON: {...}". Fragile — use JSON mode / constrained decoding in production (see [agents-tool-calling](agents-tool-calling.md)).

## What's really happening

- Prompts don't "teach" — they **set the conditional distribution**: with strong examples, the model conditions on a high-quality continuation path. The context window is a *finite-state control channel*.
- This is why **instruction-tuned** models ([instruction-tuning-rlhf](instruction-tuning-rlhf.md)) respond to prompts at all — the base GPT was trained on the next-token distribution, which does not match user intent.

## Prompt engineering as engineering

- **Determinism**: temperature, top-p, max_tokens, seed. "Why did I get a different answer?" → sampling, not a bug.
- **Injection**: user text inside your system prompt is code — the reason everyone needs a **system prompt** boundary and sandboxing (see [agents-tool-calling](agents-tool-calling.md)).
- **Budget**: prompt tokens are input cost + context (see [tokenization](tokenization.md) for token math); RAG has to *fit* its retrieved docs (see [RAG](RAG.md)).
- **Eval-driven**: a prompt is a hypothesis; measure it (see [GPT-evaluation](GPT-evaluation.md)). Prompt versioning is real engineering.

## Related

- [instruction-tuning-rlhf](instruction-tuning-rlhf.md) — what makes models *responsive* to prompts.
- [RAG](RAG.md) — prompt assembly is the delivery mechanism.
- [agents-tool-calling](agents-tool-calling.md) — prompts + tools = agents.
- [meta-learning](../automl/meta-learning.md) — the research view: "learning to learn" via context.
