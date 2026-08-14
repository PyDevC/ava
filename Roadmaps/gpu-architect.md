# GPU Architect

GPU architects design the **hardware itself**: the chip's compute units, memory system, warp/wavefront scheduling, and specialized units (tensor cores). This is the deepest hardware role — the ones who know *why* GPUs do what they do.

## Core skills

- **Computer architecture**: pipelines, caches, coherence, DRAM, SIMD/SIMT. Hennessy & Patterson is the bible.
- **GPU-specific architecture**: SM/Compute-Unit design, warp/wavefront execution, scheduling, shared/global memory hierarchy, tensor cores (matrix units), NVLink/PCIe.
- **VLSI/digital design** (for the microarchitecture side): RTL (Verilog/SystemVerilog), timing, power.
- **Performance modeling**: cycle-accurate simulation, workload analysis, roofline.
- **Firmware/driver interface**: how software (CUDA/HIP) talks to the hardware — see [[rocm-developers]] and [[hpc-engineer]] for the software side.

## What to study on NVIDIA side

- SM architecture, warp schedulers, register file (the hidden performance master), shared memory banks, memory coalescing rules, tensor core generations (Turing→Ampere→Hopper→Blackwell), TMA (Hopper), warpgroup.
- On AMD: CDNA compute units, wavefronts (64 lanes), the HSA model.

## Roadmap to get there

1. Strong CS + computer architecture fundamentals; ideally EE/digital design.
2. Read GPU architecture docs deeply (CUDA programming guide's hardware chapters, ISCA/MICRO/HotChips papers).
3. Write optimized kernels to *feel* the hardware behavior (occupancy, bank conflicts, latency hiding).
4. Work in a GPU hardware team or a performance-modeling team (GPU simulators, RTL, or software-modeling).
5. Keep software skills sharp — modern GPU architects must understand ML workloads (the tensor core is now the centerpiece).

## Related

- [[hpc-engineer]], [[rocm-developers]] — the software that runs on your hardware.
- [[ai-compiler-engineer]] — compilers must match your hardware (memory layout, instruction selection).
- [[triton-compiler-engineer]] — Triton's codegen targets exactly what you design.
