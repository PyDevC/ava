# IREE's HAL dispatch flow

When `iree-compile` finishes, the `.vmfb` contains a program of **HAL calls**. The HAL (Hardware Abstraction Layer) is IREE's device API: the compiler emits `hal.*` ops, the runtime maps them onto a concrete driver (local-sync, local-task, CUDA, Vulkan...). This note is what actually happens when you call `iree-run-module`.

## The objects HAL deals with

- **Command buffer** — a recording of device work: copy, fill, dispatch. Building one is like recording a CUDA stream / Vulkan command buffer; submitting it runs all the recorded work in order.
- **Buffers (device/host)** — memory on the device vs on the host. The runtime keeps a `hal.buffer_view` (a buffer plus shape/type descriptor); copying across device/host is an explicit `hal.command_buffer.copy` op.
- **Executable** — the compiled kernel (SPIR-V, CUDA cubin, an LLVM-cpu function...). The runtime looks it up by name from the module's executable cache via `hal.executable.lookup` — "find kernel `foo` in the compiled artifact *for this device*".
- **Push constants** — tiny per-dispatch scalar args (dims, strides) passed without touching global memory.
- **Semaphores** — the sync primitive. `hal.semaphore.signal` / `hal.semaphore.wait` order submissions across queues and devices (timeline semaphores, like CUDA event/stream semantics).

## The dispatch path

For each dispatch-able unit in the model, the compiler emits a `hal.command_buffer.dispatch` with:

1. an **executable** reference + entry point name,
2. **workgroup counts** (the grid: blocks × workgroups),
3. **push constants** (the dynamic shape/stride values for this call),
4. **bound buffers** (inputs/outputs).

At runtime the driver turns that into the real thing: for CUDA it's a kernel launch on a stream; for Vulkan it's recording a `vkCmdDispatch` into a `VkCommandBuffer`; for llvm-cpu it's an omp-parallel function call. The same compiled module is portable precisely because the HAL call set is device-agnostic.

The key idea: **each dispatch is a kernel the HAL locates on the target backend**. During compilation the Flow dialect carved the model into "dispatchers" (fusion units — usually one per linalg op or fused group); the HAL is the layer that finds and launches them at runtime.

## Sync model (timeline semaphores)

Modern IREE uses **timeline semaphores** for ordering: every submission says "wait on X" and "signal Y". Independent work can overlap across queues (that's the Stream dialect's planning). The host doesn't spin — it submits and lets the device do the waiting, which is how IREE overlaps copies and compute.

## Why it matters

This is the layer that makes "one model, many backends" work. When debugging "why is my model slow", the question becomes "how many dispatches, and what's the sync pattern between them" — the same lens as kernel launches + CUDA graphs in [Inductor](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md).

## Related

- [IREE](IREE.md) — the dialect stack this sits under.
- [Compile and run](IREE-Compile-Run.md) — where the `.vmfb` comes from.
- [Backends](IREE-Backends.md) — the drivers that execute HAL calls.
- [Fusion-Scheduler](../../PyTorchCompiler/TorchInductor/Fusion-Scheduler.md) — fusion = fewer dispatches, same idea in Inductor.
