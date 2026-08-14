# torch.compile modes and backends

`torch.compile` is a *frontend* over a pluggable backend; the `mode=` argument tunes how aggressive the backend's tuning is. This note covers what the flags actually change (extends [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md)).

## The pipeline

```
torch.compile(model, backend=..., mode=...)
   → Dynamo traces (bytecode → FX, see [[../TorchDynamo/Trace-Lifecycle]])
   → AOTAutograd builds backward (see [[../AOTAutograd]])
   → backend compiles the FX graphs (default: Inductor)
```

## The modes (Inductor)

- **`default`**: balanced. Fusion + autotune on the cheap/obvious cases.
- **`reduce-overhead`**: adds **cudagraphs** (GPU: capture the whole compiled region as a CUDA graph and replay — kills Python/launch overhead between kernels; requires static shapes).
- **`max-autotune`**: *full* autotuning — benchmarks many tile/split/vectorization variants (and template kernels like CUTLASS/TritonMM) per kernel. Best kernels, slowest compile, biggest cache. `max-autotune-no-cudagraphs` is the CUDA-graph-free variant.
- **`max-autotune-cudagraphs`**: both.
- Compile time goes: `default` ≪ `reduce-overhead` < `max-autotune`. The cache (`torch._inductor.config.cache_dir`) is what makes repeated runs cheap — warm it in CI.

## Backends

- `torch._dynamo.list_backends()` — enumerate what's installed: `inductor`, `onnxrt`, `openxla`, `tvm`, `eager`, `aot_eager`, `fx2trt`... (see [Comparison-Matrix](../DLCompilers/Comparison-Matrix.md)).
- `backend="eager"` = no compile (sanity check); `backend="onnxrt"`/`"openxla"` route through ONNX Runtime / XLA (the "use another DL compiler via torch.compile" story).
- **Custom backends**: `torch.compile(fn, backend=my_backend)` where `my_backend(gm: GraphModule, example_inputs)` returns a callable — the FX graph IS the backend contract (see [FX-Graph-IR](FX-Graph-IR.md)). `torch._dynamo.list_backends` + registering via `torch._dynamo.register_backend`.

## The integration knobs

- `torch.compiler.disable(fn)` — force eager for a function (e.g. a debug path).
- `torch.compiler.allow_in_graph(fn)` — register a Python function to be traced *as one op* (avoids graph breaks; used with custom ops, see [conditioning-on-a-python-subclass](../../Programming/python/core/conditioning-on-a-python-subclass.md)).
- `torch.compile(fullgraph=True)` — error out on any graph break (strict mode, see [GraphBreaks](TorchDynamo/GraphBreaks.md)).

## Related

- [Guards](TorchDynamo/Guards.md) — modes affect recompile behavior.
- [Fusion-Scheduler](TorchInductor/Fusion-Scheduler.md) — what `max-autotune` turns up.
- [Comparison-Matrix](../DLCompilers/Comparison-Matrix.md) — comparing backends.
- [TorchInductor](../DLCompilers/TorchInductor/TorchInductor.md) — Inductor as the default backend.
