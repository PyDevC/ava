# TorchInductor

TorchInductor (usually just "Inductor") is PyTorch's default compilation backend. It is the `inductor` backend used by `torch.compile()` with no arguments. It turns the FX graphs produced by TorchDynamo into generated GPU kernels (Triton) and CPU kernels (C++/OpenMP).

> See [EntryPoint](../../../PyTorch/Compilers/TorchInductor/EntryPoint.md) for the entry point / call chain.

## Compiler stack

The whole `torch.compile` stack for the inductor backend:

```
Python eager code
   |  TorchDynamo traces
   v
FX Graph (ATen ops)
   |  AOTAutograd + PrimTorch: capture fwd+bwd, decompose into a smaller op set
   v
Inductor Graph Lowerings
   |  remove views/broadcasting, simplify indexing, layout tuning, loop order
   v
Inductor Scheduling
   |  horizontal/vertical fusion, reduction fusions, tiling, memory planning, buffer reuse, autotune
   v
Wrapper Codegen  (outer code that calls kernels and allocates memory)
   |
   v
Backend Codegen
   Triton (GPU) / C++ (CPU)
```

Key source files under `torch/_inductor/`: `compile_fx.py` (entry), `graph.py` (IR building), `ir.py` (IR node definitions), `scheduler.py` (fusion), `codegen/triton.py` and `codegen/cpp.py` (backends).

## Define-by-run loop-level IR

Inductor does not use a traditional text IR. It uses a Pythonic "define-by-run" IR: IR nodes are Python callables (`inner_fn`) that take `sympy.Expr` indexing expressions and emit `ops.*` operations. Codegen works by changing the implementation of `ops.*` and re-running the IR. Example IR for `x.permute(1,0) + x[2,:]` is a function `inner_fn(index: List[sympy.Expr])` that loads from both tensors with sympy indexing formulas and returns the sum.

This makes it very easy to write lowings - you just express an op in terms of the ops interface and codegen picks up the new backend automatically.

## Dynamic shapes

Dynamic shapes and strides are first class via the SymPy symbolic math library. Shapes that aren't statically known become `sympy.Symbol`s that flow through the whole program. Memory loads/stores are expressed as sympy indexing formulas over iteration variables and symbolic sizes. Inductor introduces **guards** that lift assumptions to the top of the graph; if a later input violates a guard, it triggers a recompile.

## Fusion and scheduling

The scheduler decides what fuses together:

- `Scheduler.can_fuse(buf0, buf1)` - tests if two buffers can be fused
- `Scheduler.score_fusion(buf0, buf1)` - returns (fusion categories, estimated memory bandwidth saved, distance in graph); controls the order since some fusions block others

Pointwise ops can fuse with other pointwise ops, reductions, and with matmul/conv templates (epilogue fusions). Matmul/conv use hand-written templates with auto-generated epilogue fusion, everything else is generated from scratch from the IR.

## Backends and features

- **GPU**: generates Triton kernels, uses CUDA Graphs (`cudagraphs`) to cut launch overhead for static shapes.
- **CPU**: generates C++ with OpenMP parallel loops; Intel has contributed oneDNN/MKLDNN fusions and weight prepacking for Conv/GEMM.
- Autotuning: heuristics + benchmarking pick the best Triton configs (block sizes, etc.).
- AOT mode and other experimental modes exist via config flags.

## References

- "TorchInductor: a PyTorch-native Compiler with Define-by-Run IR and Symbolic Shapes": https://dev-discuss.pytorch.org/t/torchinductor-a-pytorch-native-compiler-with-define-by-run-ir-and-symbolic-shapes/747
- PyTorch 2.0 Hot Chips talk (pipeline overview): https://hc2023.hotchips.org/assets/program/tutorials/ml/PyTorch%202.0.pdf
