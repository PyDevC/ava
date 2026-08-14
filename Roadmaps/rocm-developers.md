# ROCm Developers

ROCm is AMD's open-source GPU compute platform (CUDA counterpart). ROCm developers write kernels, libraries, and driver/runtime software for AMD GPUs (Instinct/MI-series, Radeon).

## Core skills

- **Heterogeneous programming**: HIP (the C++ dialect that maps to both CUDA and AMD), ROCm libraries.
- **GPU architecture**: AMD CDNA/RDNA — compute units, wavefronts (vs NVIDIA warps), memory hierarchy.
- **Low-level systems**: device driver stack, ROCm runtime (`rocr`), HSA (Heterogeneous System Architecture) — the underlying model.
- **Kernel optimization**: occupancy, vectorization, `__shfl`-like lane ops, local memory.
- **Compilers**: LLVM backend for AMDGPU, code object format (COFF/AMDGCN), assembler tooling.
- **The PyTorch/AI connection**: ROCm provides the PyTorch build path for AMD (see [Explore](../PyTorch/Explore.md)'s CI notes and the AMD runners).

## The ecosystem to learn

- **HIP** — the language; portable to CUDA via HIPify.
- **rocBLAS, rocFFT, rocSPARSE, rocRAND** — library analogs of cuBLAS/cuFFT.
- **MIOpen** — the CNN library (cuDNN analog).
- **rccl** — the collective-comms library (NCCL analog) for distributed training.
- **ROCm stack layers**: user code → HIP runtime → HSA → kernel driver → GPU.

## Roadmap to get there

1. C++ + one GPU programming model (CUDA first is fine — HIP is nearly 1:1).
2. Learn the AMD GPU model (wavefronts, memory tiers) — see [gpu-architect](gpu-architect.md).
3. Write HIP kernels; learn MIOpen/rocBLAS patterns.
4. Contribute to ROCm or a framework's ROCm backend (PyTorch ROCm wheels).

## Related

- [gpu-architect](gpu-architect.md) — the hardware knowledge underneath.
- [ai-framework-engineer](ai-framework-engineer.md) — where ROCm skills plug into frameworks.
- [hpc-engineer](hpc-engineer.md) — systems-level parallel computing overlap.
