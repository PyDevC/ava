# Debug With TorchDynamo

TorchDynamo is the tracing frontend that turns Python bytecode into a graph. When something is slow, wrong, or unexpectedly uncompiled, this is where you start debugging.

## First: find graph breaks

```bash
TORCH_LOGS=dynamo python my_script.py
```

This prints the guard conditions, the trace events, and — critically — any `[graph break]` lines. Each break tells you *why* tracing stopped (see [[GraphBreaks]]). For a quick summary:

```python
from torch._dynamo.explain import explain
explain_result = explain(fn, *args)
explain_result.break_count      # total breaks
explain_result.break_reasons    # what caused the first ones
```

## Deeper logs

Set several logs at once:

```bash
TORCH_LOGS="dynamo,graph_breaks,guards,bytecode" python ...
TORCH_LOGS=+dynamo python ...     # verbose mode
```

- `graph_breaks` — exact break reasons and stack traces.
- `guards` — what shape/dtype facts Dynamo is guarding on; if guards are too weak (or re-trigger often) recompilation storms happen.
- `bytecode` — the bytecode being traced before/after.

## Config flags

`torch._dynamo.config` has a bunch of toggles for debugging:

```python
import torch._dynamo as dynamo
dynamo.config.log_level = logging.DEBUG
dynamo.config.verify_correctness = True    # run traced vs eager and compare
```

## Isolate the problem function

- Set `TORCHDYNAMO_DEBUG_FUNCTION=my_function_name` to only emit debug output for a specific function — a lifesaver in huge scripts.
- If a function breaks or miscompiles, test it standalone: does it work eagerly? does `torch.compile(fullgraph=True)` error?
- `torch._dynamo.config.suppress_errors = True` falls back to eager on any Dynamo error, so your script keeps running while you debug.

## When the generated code is wrong

If Dynamo traced fine but results differ, the bug is usually downstream (inductor lowering or codegen). Use:

```bash
TORCH_LOGS=+inductor python ...
TORCH_COMPILE_DEBUG=1 python ...   # dumps graphs, IR and codegen artifacts to /tmp/torchinductor_*/debug
```

Also `torch._dynamo.utils.compile_times()` reports where compile time went — useful when compilation itself is the bottleneck.

## The minifier

PyTorch ships `torch.utils.collect_env` and a **compile error minifier** (`torch._dynamo.utils` / `scripts/compile_tests`). You can run:

```bash
python -m torch._dynamo.utils.collect_env   # environment sanity
```

and use `torch._dynamo.config.repro_after = "dynamo"` to get a self-contained reproduction script for a failure.
