# Repos worth studying

The reference implementations of the ML-compiler world — the repos I read when I want to see how a real system is built. Links only, with a one-line "what to study in it".

## Frameworks / compilers

- **PyTorch** — https://github.com/pytorch/pytorch (ATen/c10, dispatch, torch.compile). Start points: [Explore](../PyTorch/Explore.md), [Build-From-Source](../PyTorch/Build-From-Source.md).
- **TorchInductor** — in-repo under `torch/_inductor/`: the codegen path from FX graph → Triton/C++ (see [PLAN](../Compilers/PyTorchCompiler/)).
- **Triton** — https://github.com/triton-lang/triton (the kernel language + compiler). Study: the IR and the GPU backends.
- **IREE** — https://github.com/iree-org/iree (MLIR-based end-to-end DL compiler + runtime, see [IREE](../Compilers/DLCompilers/IREE/IREE.md)).
- **MLIR / llvm-project** — https://github.com/llvm/llvm-project (dialects, passes, the pass framework everything above uses).
- **ONNX Runtime** — https://github.com/microsoft/onnxruntime (the graph optimizer + execution-provider model, see [ONNXRuntime](../Compilers/DLCompilers/ONNXRuntime.md)).
- **TVM** — https://github.com/apache/tvm (the old-school search-based auto-tuner, see [TVM](../Compilers/DLCompilers/TVM.md)).
- **XLA / StableHLO** — https://github.com/openxla/xla (HLO, the Google stack, see [XLA-StableHLO](../Compilers/DLCompilers/XLA-StableHLO.md)).

## Examples / references

- **TensorRT samples** — https://github.com/NVIDIA/TensorRT (NVIDIA's sample code: deployment, ONNX integration).
- **CUDA samples** — https://github.com/NVIDIA/cuda-samples (kernel idioms to port to HIP).
- **HIP / ROCm samples** — https://github.com/ROCm/rocBLAS and the ROCm ecosystem repo (the AMD library patterns).

## How to read them

Large repos reward the [how-to-explore-large-repos](../OpenSource/how-to-explore-large-repos.md) approach: start from an entry point (a paper, a build target, a specific op), read only the seam you need, and go deeper on demand rather than top-to-bottom.

## Why it matters

These are the systems my notes describe; reading the real source is where "I understand the concept" becomes "I understand the implementation". The [paper-library](paper-library.md) is the reading list; this is the code list.

## Related

- [PLAN](./) — the Store area this lives in.
- [how-to-explore-large-repos](../OpenSource/how-to-explore-large-repos.md) — the reading strategy.
- [glossary](glossary.md) — the acronyms each repo's docs assume.
