# Torch Inductor Entry point

When you call `torch.compile()`, without any arguments then it calls the inductor backend.

The main entry point for this backend is the `compile()` function in `torch/_inductor/__init__.py` which just calls the `compile_fx` function from `compile_fx.py` file.

## The full call chain from torch.compile

1. `torch.compile(fn, backend="inductor")` sets up a TorchDynamo context that rewrites/instruments the Python frame via `torch/_dynamo/eval_frame.py`. Nothing compiles at this line yet for a given graph - tracing happens lazily on first call.
2. On first invocation, Dynamo traces the function bytecode into a `torch.fx.GraphModule`, capturing the eager ops as nodes.
3. Dynamo passes this FX graph to the registered backend (`inductor`), which calls into `torch/_inductor/__init__.py:compile()`.
4. `compile()` calls `compile_fx(gm, example_inputs)` in `torch/_inductor/compile_fx.py`. The docstring literally says "Main entry point for compiling given FX graph". This function orchestrates the whole end-to-end compilation:
   - runs AOTAutograd to capture the backward graph alongside the forward,
   - runs pre-grad passes (decompositions into core ATen ops, pattern matching, e.g. `mkldnn` fusions on CPU),
   - calls back into `_compile_fx_inner` -> `fx_codegen_and_compile` for the actual lowering.
5. Lowering converts ATen ops into Inductor's IR (the define-by-run IR with symbolic shapes), applies fusion and optimizations, and finally codegens Triton kernels (GPU) or C++/OpenMP kernels (CPU), which are compiled and cached.

So the "entry point" is really three layered things:

- User facing: `torch.compile()` -> Dynamo
- Backend facing: `torch/_inductor/__init__.py:compile()`
- Graph compiling: `compile_fx()` in `compile_fx.py`

## Debugging the flow

- `TORCH_LOGS=+inductor` or `TORCH_LOGS=+dynamo` to see logs
- `TORCH_LOGS=+help` lists all available log categories
- The evolution chain of an op: `torch.neg` (Python) -> `torch.ops.aten.neg.default` (FX graph node) -> `ops.neg` (IR node) -> `tmp2 = -tmp1` (generated kernel)
- Comparing backends `torch.compile(fn, backend="eager")` vs `aot_eager` vs `inductor` helps you narrow down which stage broke

## References

- <https://github.com/pytorch/pytorch/blob/main/torch/_inductor/__init__.py>
- <https://github.com/pytorch/pytorch/blob/main/torch/_inductor/compile_fx.py>
