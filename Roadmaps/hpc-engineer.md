# HPC Engineer

High-Performance Computing engineers make **scientific and compute-heavy code fast** on big machines: supercomputers, GPU clusters, massively parallel systems. HPC predates AI and the AI-training stack is basically HPC applied to ML.

## Core skills

- **C/C++/Fortran** (HPC's native languages) + assembly-level intuition.
- **Parallel programming**: MPI (distributed), OpenMP (shared-memory threads), SIMD/vectorization, and GPU (CUDA/HIP) — see [rocm-developers](rocm-developers.md).
- **Performance engineering**: profiling (perf, vtune, ncu), roofline analysis, cache/memory optimization, algorithmic optimization (loop tiling, blocking, fusion).
- **High-performance math**: BLAS/LAPACK, FFT libraries, linear solvers.
- **Cluster/scheduler knowledge**: SLURM/PBS, InfiniBand networking, Lustre filesystems.
- **Compilers**: -O3/`-march=native`, auto-vectorization, OpenMP pragmas, PGO.

## The connection to AI

The skill set is nearly identical to training-acceleration work: kernel fusion (see [TorchInductor](../Compilers/DLCompilers/TorchInductor/TorchInductor.md)), MPI ↔ NCCL/RCCL collectives, HBM/register pressure tuning, memory-bound vs compute-bound reasoning.

## Roadmap to get there

1. Strong C/C++; understand cache hierarchy and memory bandwidth.
2. SIMD + OpenMP (easy win, runs anywhere).
3. MPI for distributed; run on a small cluster.
4. GPU: CUDA (then ROCm for AMD) — see [gpu-architect](gpu-architect.md).
5. Performance methodology: profile first, optimize what's hot, verify speedup.

## Related

- [gpu-architect](gpu-architect.md) — the hardware model you're optimizing for.
- [ai-compiler-engineer](ai-compiler-engineer.md) — compilers that do the fusion/vectorization automatically.
- [devops-engineer](devops-engineer.md) — the cluster/ops side of the same infrastructure.
