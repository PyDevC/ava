# Agents and tool calling

An **agent** is an LLM in a loop: it decides an action, calls a **tool**, observes the result, and decides again. The model is the planner; tools are its hands.

## The loop

```
while not done:
    action = model(prompt + conversation + tool_results)   # e.g. "call tool search(query=...)"
    result = execute(action)                                # the real world responds
    append(result)                                          # feed back into context
```

Three parts: (1) the **tool schema** the model can choose from, (2) the **execution environment**, (3) the **loop control** (when to stop, how many steps, error handling).

## Tool calling (function calling)

- The model emits a structured call (JSON with function name + args) instead of free text; the runtime dispatches it. The API contract: `tools=[...]` in the request, `tool_calls` in the response.
- **Constrained decoding** makes this reliable: grammar/JSON schema enforcement at generation time so the model *can't* emit malformed calls (inference-time, see [[../deeplearning/model-optimization-for-inference]]).
- Tool results are **text** appended to the context — the model sees "the search returned: ..." and continues. Everything is still just tokens (this is why [[RAG]] and tools are the same mechanism: context assembly).

## Why it's hard

- **Long horizons**: errors compound; the model forgets its own reasoning (context is the memory — see [[prompting-in-context-learning]]).
- **Latency & cost**: each loop iteration is a full model call. Tool calls dominate wall-clock time in agent apps.
- **Safety & injection**: the tool output is untrusted input — a search result telling the model to "ignore previous instructions" is real. Sandboxing, allow-listing, and human-in-the-loop are mandatory (this is the systems angle: permissions, timeouts, rate limits).
- **Determinism**: the model can loop forever, call the same tool repeatedly, or hallucinate a tool. Loop budgets + guardrails are table stakes.

## The agentic stack

- **Frameworks**: LangChain, LlamaIndex, AutoGPT; the *underlying* pattern is just prompt + tools + loop.
- **Dedicated agent models** (e.g. "agentic" fine-tunes) are tuned specifically to use tools well.
- **Memory**: short-term (context) vs long-term (vector store of past observations → the RAG connection again, [[RAG]]).

## Related

- [[prompting-in-context-learning]] — the prompt machinery the loop runs on.
- [[RAG]] — retrieval is the simplest "tool".
- [[GPT-evaluation]] — how to score an agent: task success rate, steps taken, cost per task.
