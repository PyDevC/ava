# Guards and recompilation

Guards are the **contract Dynamo records when it compiles a region**: "this compiled code is valid *only if* these facts about the inputs still hold." When a guard fails at runtime, Dynamo **recompiles** for the new conditions. Recompilation storms are the #1 `torch.compile` performance gotcha.

## What guards capture

- **Shapes**: `s0 == 128`, `s1 > 0` (via symints, see [[SymPy-Symbolic-Shapes]]), tensor `size()`.
- **Dtypes / layout / device** of inputs.
- **Python object identity / class / dict-contents**: `self is <module instance>`, `config is dict with keys {...}`.
- **`requires_grad`** on tensors (a *guard*: eager/backward mode changes compile path — this is why `torch.no_grad()` / eval-mode toggles recompile).

A guard list is attached to each compiled artifact; at call time the inputs are checked against it. If all pass → reuse; any fail → **recompile** (new specialization).

## Why recompilation hurts

- Recompiling a big graph costs seconds (capture + codegen + autotune). **Guards fine, compile slow** is the classic "first call slow" symptom.
- **Guards failing every call** → compile-every-time → *slower than eager*. Symptoms: alternating shapes in a loop (batch sizes vary), `python_list_length` guards on a data-dependent list, tensor-`isinstance`-to-`Python` toggling (see [[../../Programming/python/core/conditioning-on-a-python-subclass]]).

## The knobs (from [[DebugWithTorchDynamo]])

- `torch._dynamo.config.dynamic_shapes=True` — opt into dynamic dims so a range of sizes shares one compile (fewer, broader guards) instead of per-size recompiles.
- `torch._dynamo.config.recompile_limit` (default 8) — recompiles allowed before Dynamo falls back to eager for that frame; raise it to see if a few extra compiles actually help, lower it to cap compile time.
- `TORCH_LOGS=recompiles` / `torch._dynamo.config.recompile_reason_log` — see *why* each recompile happened. This is the first thing to check when compile "isn't helping".
- Use **static shapes in eval** (`torch.no_grad`, fixed input sizes, batch one shape) so the guards are trivially stable.

## The mental model

```
compile(region, guards) → [guards hold? → run compiled
                          [guard fails   → recompile(region, new_guards)]
```

"Make your guards stable" = make shapes/dtypes/object-identity stable across calls. Everything else (tensor values) is guard-free — values don't trigger recompiles.

## Related

- [[Trace-Lifecycle]] — where guards are collected during capture.
- [[SymPy-Symbolic-Shapes]] — the symbolic shapes guards reason about.
- [[GraphBreaks]] — breaks and guards are the two "why am I slow" levers.
- [[DebugWithTorchDynamo]] — the logging that shows guards firing.
