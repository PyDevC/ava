# TVM

TVM (Apache) is the **historical baseline** of modern DL compilers — many of the ideas Inductor, XLA, and IREE now use (graph IR → lowered loops → autotuned kernels) trace straight back to it. Even if you never use it, understanding TVM explains *why* the others look the way they do.

## The three layers

1. **Relay** — the graph-level IR. High-level compute graph with functional ops (relay ops on tensors). This is the "frontend IR" layer (see [[README]] for the shared pipeline picture). Frontends (TF/PyTorch/ONNX) lower into Relay.
2. **TE (Tensor Expression)** — the expression/loop-level IR. Describe a compute per-element:
   ```
   C = te.compute((n, m), lambda i, j: te.sum(A[i,k]*B[k,j], axis=k))
   ```
   A schedule then says *how* to run it (tiling, vectorization, parallel, reorder). Schedule = the compiler's optimization decision, expressed by the user or by AutoTVM.
3. **AutoTVM / Ansor** — automatic scheduling/search. AutoTVM tunes cost-model-parameters (tile sizes, unroll factors); **Ansor** goes further and searches whole schedules from scratch ("evolutionary" template-free search) using a learned cost model + measurements on the actual device. This "search the schedule" idea is exactly what `torch.compile`'s autotuning does (see [[TorchInductor/TorchInductor]]).

## Codegen targets

TVM generates kernels for CUDA, ROCm, OpenCL, Metal, Vitis/Vivado (FPGA), and LLVM ("llvm" backend → NVPTX/CPU). The key API is the **TVM runtime** (`tvm.contrib`), the C++ runtime that loads and runs compiled kernels — the "IR → codegen → runtime" chain of the shared pipeline, done before IREE's `.vmfb` era.

## Why it matters now

- **The template**: graph IR + tensor expressions + schedule search + a runtime is the skeleton of IREE, XLA, and Inductor. Read TVM's docs once and the other compilers stop being a black box.
- **Autotuning**: Ansor/autoTVM's cost-model-driven search is where `torch.compile`'s `mode="max-autotune"` ideas come from (see [[TorchInductor/TorchInductor]]).
- The MLIR world largely superseded its IR plumbing (TVM's Relay → StableHLO/tosa → linalg in newer tools), but TVM as a *runtime+search* project is still alive (used in ARM's Corstone and various edge stacks).

## Related

- [[README]] — the shared pipeline it instantiates.
- [[TorchInductor/TorchInductor]] — the modern auto-tuning descendant.
- [[XLA/StableHLO]] — the graph-IR competitor.
- [[../MLIR/Dialects/linalg]] — the modern equivalent of TE's loop level.
