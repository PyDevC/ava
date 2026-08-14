# IREE backends (when to pick which)

`iree-compile` takes one flag to choose the codegen backend: `--iree-hal-target-backends=llvm-cpu`, `cuda`, `vulkan-spirv`, `rocm`, or `webgpu`. Same frontend, same pipeline, different codegen. This note is when I'd pick each.

## llvm-cpu

- **What**: lowers linalg → loops/vectorization → LLVM IR → native machine code. Runs on the CPU, no GPU needed.
- **Tuning**: `--iree-llvmcpu-target-cpu-features=+avx2`-style flags, plus IREE's **microkernels** (specialized vector/assembly kernels for common ops like pack/unpack, GEMM peels).
- **When**: dev machine (this is what I use to iterate on a model), any CPU-only deployment, CI. The fastest way to sanity-check that a model compiles and runs.

## cuda

- **What**: linalg → CUDA kernels; matmul/conv are offloaded to **cuBLAS/cuDNN**, and the tiled elementwise/reduction/fused parts use **Triton-generated kernels**.
- **When**: datacenter inference where the GPU is the whole point. The mature path — this is IREE's production GPU story.

## vulkan-spirv

- **What**: linalg → SPIR-V, dispatched through Vulkan compute. Vulkan is the most portable compute API — it runs on AMD/NVIDIA/Intel/Apple GPUs.
- **When**: portability or mobile/embedded. Ship SPIR-V once, run anywhere Vulkan exists. Cost: codegen quality varies by driver, less tuned than cuda.
- **metal**: same idea, via MoltenVK → Apple Metal for macOS/iOS.

## rocm

- **What**: linalg → HIP for AMD GPUs (MI-series). The AMD answer to the cuda backend.
- **When**: AMD GPU fleets.

## webgpu

- **What**: compiles to WebGPU shaders for in-browser inference.
- **When**: wasm/browser deployment — the artifact runs in the browser with the small web runtime.

## When to pick which (my rule of thumb)

- Dev machine / iterating: **llvm-cpu** — zero GPU setup, fast iteration.
- Datacenter NVIDIA: **cuda**.
- Datacenter AMD: **rocm**.
- Need to run anywhere (mobile, browsers, unknown GPUs): **vulkan-spirv**.
- Never assume — benchmark on the real target; the driver matters as much as the compiler.

## Why it matters

The whole point of IREE's design: **compile once per target, same runtime call**. I can prototype on llvm-cpu, then rebuild the `.vmfb` with `cuda` for production without touching the model. See [Compile and run](IREE-Compile-Run.md) — the only difference is the flag.

## Related

- [IREE](IREE.md) — backend list at a glance.
- [Compile and run](IREE-Compile-Run.md) — the command that takes the flag.
- [HAL dispatch](IREE-HAL-Dispatch.md) — how the backend executes dispatches.
- [Comparison-Matrix](../Comparison-Matrix.md) — IREE vs the other compilers.
