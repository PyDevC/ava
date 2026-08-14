# PyTorch/Compilers/TorchInductor — Plan

## Goal

Notes on **TorchInductor as the entry point of `torch.compile`** — the call path and the user-facing story.

## What's here

- [EntryPoint](EntryPoint.md) — `torch.compile()` → `torch/_inductor/__init__.py:compile()` → `compile_fx()` → AOTAutograd → lowering → Triton/C++; debugging via `TORCH_LOGS`.

## What to add next (see also [PLAN](../PLAN.md) and [TorchInductor](../../../Compilers/DLCompilers/TorchInductor/PLAN.md))

- [ ] AOTAutograd in detail (how backward gets compiled)
- [ ] `torch.compile` modes and backends user guide
- [ ] Dynamic shapes / symbolic shapes handling
- [ ] Reading a real generated kernel end-to-end
- [ ] Profiling compiled vs eager — where wins/losses land
