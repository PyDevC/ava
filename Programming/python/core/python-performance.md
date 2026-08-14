# Python performance

"When is Python the bottleneck, and what do I do?" — the profiling-first discipline that keeps Python fast enough for real systems. The answer is almost never "rewrite in C" first; it's "profile, then fix the one hot thing".

## Profiling first, always

- **`cProfile`** — function-level CPU profile (run, dump, `pstats`); the standard first pass.
- **`py-spy`** — sample an *already-running* process (no code change, safe on prod): "what is my training loop stuck on?" (often: GIL contention, a blocking call, a worker that died — see [[gil-threading]]).
- **`perf`/`pyperf`**, **`memory_profiler`**, **`objgraph`** (see [[python-memory]]) for the memory side.
- The rule: measure, form a hypothesis, *then* optimize. The top 1 function is where the time goes; guessing wastes it.

## The optimization ladder (cheapest → costliest)

1. **Algorithm/data structure**: the best fix — a dict instead of a list, a set-membership check, a sorted-insert vs linear scan. Python's builtins (`sorted`, `sum`, `dict`, `set`) are C-fast; the *algorithm* is what you control.
2. **Micro-fixes**: local-variable caching (see [[frames-namespaces]]), `__slots__`, `@lru_cache`, replacing method calls in loops, `array`/`numpy` for numeric work, `itertools` over hand-rolled loops.
3. **Concurrency**: threads for I/O, processes for CPU (see [[gil-threading]]). `concurrent.futures` / `asyncio` for the I/O shape.
4. **Offload to C/Numpy**: vectorize with `numpy`, or a pybind11 extension (see [[python-c-api]]). The modern "slow loop" fix is 90% "make it a NumPy op".
5. **Different interpreter**: PyPy (JIT), or 3.11+ (the specializing interpreter, see [[bytecode]]), 3.13 free-threading for GIL-free code. Rarely the right first move, often the right *last* move.

## The Python-bottleneck tell

- Profile shows >50% time in **Python frames** (not C calls) → the interpreter loop is the cost → vectorize/offload.
- Profile shows **C-extension time** (numpy, torch kernels) → that's real compute; the fix is algorithm or *fewer kernel launches* → `torch.compile` territory (see [[../../Compilers/PyTorchCompiler/PLAN]]).
- **GIL contention** (threads fighting) → restructure (processes or GIL-free ops, see [[gil-threading]]).

## The training-loop case study

PyTorch training is Python-glue + C-kernels. The classic win order: `torch.compile` (fewer launches, see the compiler plan) → batch on GPU (`pin_memory`, non-blocking) → workers (see [[PyTorch/Performance]]) → and *then* micro-optimize the Python loop (they're rarely the problem once kernels dominate).

## Related

- [[frames-namespaces]] — the lookup model you're optimizing.
- [[gil-threading]] / [[python-memory]] — the two runtime constraints.
- [[python-c-api]] — the offload escape hatch.
- [[../../Compilers/PyTorchCompiler/PLAN]] — compiled code as the big win.
