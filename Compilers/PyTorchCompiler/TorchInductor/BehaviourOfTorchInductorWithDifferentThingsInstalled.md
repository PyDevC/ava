# Behaviour of TorchInductor with different things installed

Based on the libraries available, torch inductor is capable to generating the code for various things.

TorchInductor inspects the environment at compile time and picks its codegen strategy accordingly. The rule of thumb: **the more good libraries you have, the faster the generated code** — but it always has a correct fallback.

## What changes with what installed

- **Triton (GPU)** — if Triton is installed and a CUDA GPU is present, inductor generates Triton kernels for pointwise/reduction/fused ops. Without Triton, CUDA falls back to slower eager CUDA ops (or C++/CUDA codegen via `--generate-cuda` in newer versions).
- **Triton versions** — inductor pins minimum Triton versions per PyTorch release; too-new/too-old Triton can disable the Triton path with a warning.
- **C++ compiler + OpenMP (CPU)** — for CPU, inductor generates C++ with `#pragma omp` loops for parallelism. No OpenMP → single-threaded C++ still works but is slower.
- **cuDNN / oneDNN (MKL-DNN)** — enables the conv/batch-norm/matmul **templates**: instead of triton kernels, inductor emits `aten::cudnn_convolution` / `oneDNN` calls, which beat hand-rolled triton for large convs.
- **CUDA Graphs (cudagraphs)** — when the graph is capture-safe, inductor re-runs it via CUDA Graph capture to cut kernel launch overhead (huge for many small kernels). Enabled by `torch.compile(mode="reduce-overhead")`.
- **CUTLASS** — optional; with the `torch._inductor` CUTLASS path it can generate CUTLASS MMA templates for matmuls on supported GPUs.
- **torchvision / other extensions** — if an op is `torch.ops.*` from an extension, inductor either decomposes it (if there's a decomposition) or leaves it as a call into the eager op inside the graph.

## How to inspect the choice

```python
import torch
torch._inductor.config.debug = True
```

or with logs:

```bash
TORCH_LOGS=+inductor python my_script.py
```

The log shows which codegen path was taken, what kernels were generated, and any warnings like "Triton not found, using eager fallback".

## Practical implications

- CPU machines: install a good `g++`/`icc` + `libomp` to get OpenMP parallelism, and oneDNN wheels for conv-heavy models.
- GPU machines: make sure Triton is the version matching your PyTorch, and use `mode="reduce-overhead"` to unlock cudagraphs.
- If something suddenly slows down after an install/upgrade, check the `+inductor` log for a silent fallback — that's almost always the cause.
