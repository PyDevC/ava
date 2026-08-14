# DL compiler comparison matrix

The payoff of this directory: one table that maps the compilers side by side. The theme — **they all implement the same pipeline** (frontend → IR → optimization → lowering → codegen → runtime, see [[README]]) — with different choices at each step.

| Compiler | Frontend input | Graph IR | Optimizations | Codegen | Runtime | Dynamic shapes | Open source |
|---|---|---|---|---|---|---|---|
| **TorchInductor** | PyTorch (FX from Dynamo) | its own loop-level IR | fusion, autotuning (mode=max-autotune) | **Triton** (GPU) / C++ (CPU) | in-process, reuses PyTorch | yes (tensor-level, recompile on shape change) | yes |
| **XLA** | JAX/TF → **StableHLO** | HLO (lmhlo) | aggressive fusion, layout, shape polymorphism | Triton/CUDA, LLVM CPU, TPU | standalone runtime | shape-polymorphism (per-dim symbols) | yes |
| **IREE** | PyTorch/JAX/TF → **StableHLO/tosa** | linalg (MLIR) | transform/tile/fuse/vectorize | LLVM CPU, CUDA, Vulkan/SPIR-V, Metal | `.vmfb` modules + HAL | compiler-time via linalg; runtime modules | yes |
| **TVM** | TF/PyTorch/ONNX → Relay | Relay + TE | schedule search (AutoTVM/Ansor) | LLVM, CUDA/ROCm/OpenCL, FPGA | TVM runtime | limited (recompile) | yes |
| **ONNX Runtime** | **ONNX** | its own graph | fusion, constant folding, quantization | vendor libs + own kernels, EP plugins (TensorRT, OpenVINO...) | sessions/EPs | yes (dynamic input shapes) | yes (core) |
| **TensorRT** | ONNX/PyTorch/Prototxt | TRT graph | layer fusion, precision, profiling | NVIDIA CUDA kernels | TRT engine runtime | via optimization profiles | no |
| **DISC** | PyTorch (TorchScript) | own IR (MLIR-based, XLA heritage) | graph + kernel fusion | CUDA/ROCm | torch runtime + launcher | **strong** (shape speculation) | yes |

## Reading the matrix

- **The frontend-IR convergence**: the whole ecosystem is consolidating on **StableHLO/tosa → linalg** (IREE, XLA, torch-mlir) — see [[../MLIR/Dialects/tosa-stablehlo]]. TorchInductor is the notable exception (own IR, because it must consume PyTorch's live graph).
- **The split is "executor vs compiler"**: ORT/TensorRT *fuse + dispatch vendor kernels*; TVM/XLA/IREE/Inductor *generate kernels* (via Triton, LLVM, or hand-rolled). Generating beats dispatching once you need custom fusion (see [[ONNXRuntime]]'s note on exactly this).
- **Dynamic shapes** is the differentiator every team trips on: XLA's shape-polymorphism, DISC's speculation, Inductor's recompile-on-change — same problem, three answers (see [[../DISC/DISC]]).
- **Runtime story**: Inductor stays *in-process* with PyTorch (no separate artifact); IREE/TensorRT/ORT produce loadable artifacts (`.vmfb`/`.engine`/sessions). That's the "ships to a different process/device" decision.

## Which to use when

- **Same process, PyTorch, want it fast**: `torch.compile` (Inductor) — zero-pipeline-change, best ergonomics.
- **Need portability / separate runtime / non-PyTorch**: IREE or ONNX Runtime.
- **NVIDIA GPU, production serving, latency-bound**: TensorRT (via ONNX export).
- **JAX/TF ecosystem**: XLA already IS the backend.
- **Exotic hardware / custom kernels**: TVM or MLIR-by-hand (IREE gives you that too).

## Related

- [[README]] — the shared pipeline this table is a projection of.
- [[../MLIR/Dialects/tosa-stablehlo]] — the convergence frontend IR.
- Each compiler's note in this directory.
