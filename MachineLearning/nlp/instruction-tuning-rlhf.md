# Instruction tuning and RLHF

Pretrained LLMs predict the *next token* — they are not useful chatbots. **Post-training** converts a next-token predictor into an instruction-following assistant. There are three increasingly-sophisticated stages (see [GPT-decoder-only-models](GPT-decoder-only-models.md) for where this sits in the pipeline).

## Stage 1: SFT (supervised fine-tuning)

- Train on human-written (or model-generated + curated) `instruction → response` pairs.
- Standard cross-entropy (see [loss-function](../algorithms/loss-function.md)) on the response tokens. Cheap, ~1k–100k examples suffice.
- Turns "predict what the internet would say" into "answer the user's question". But: the model only imitates — it hasn't learned *preferences*.

## Stage 2: Reward modeling

- Collect **human preferences**: which of two responses is better for a given prompt (this is where RLHF's "HF" — human feedback — enters).
- Train a **reward model `r(x, y)`**: a frozen LLM with a regression head that scores a response. Binary preference loss (Bradley-Terry): `maximize log σ(r(x, y_win) − r(x, y_lose))`.

## Stage 3: RL — policy optimization

- Treat the LLM as a policy: it samples responses, gets a reward from the reward model.
- **PPO** (the classic): maximize reward while staying close to the SFT model (KL penalty against it, so it doesn't degenerate into gibberish that exploits the reward model).
- **DPO (Direct Preference Optimization)**: the modern shortcut — because preference optimization can be re-written as a *closed-form* reward, you can train **directly on preference pairs** with no separate reward model or RL loop. Hugging Face `trl` implements both (`PPOTrainer`, `DPOTrainer`).

## Why RLHF matters / its pitfalls

- Aligns outputs with "what humans judge best" — helpfulness, honesty, safety — things raw next-token loss can't capture.
- **Reward hacking**: the model finds responses that score high but are terrible (it's optimizing the proxy, not humans). The KL penalty and human-in-the-loop eval exist for this reason.
- **Alignment tax / preference collapse**: over-optimization makes outputs bland.

## Related

- [GPT-decoder-only-models](GPT-decoder-only-models.md) — the base model this post-processes.
- [GPT-evaluation](GPT-evaluation.md) — RLHF-trained models *must* be evaluated by humans or better-aligned judges, since standard metrics (BLEU/ROUGE) don't capture preference quality.
- [RAG](RAG.md) — instruction-tuned models still hallucinate; retrieval grounds them.
