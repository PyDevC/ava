# IREE: compiling and running a real model

The point of this note: take an actual model, run it through `iree-compile`, and get a `.vmfb` that `iree-run-module` executes. Everything below is copy-paste-able (I ran it on a plain x86 laptop with no GPU — `llvm-cpu` needs none).

## Install

```bash
python -m pip install iree-compiler iree-runtime
python -m pip install torch torch-mlir        # only needed for the PyTorch path
```

`iree-compiler` and `iree-runtime` are separate wheels — the compiler produces the artifact, the runtime executes it. That split is the whole AOT story from [IREE](IREE.md).

## PyTorch path (via torch-mlir)

torch-mlir traces a PyTorch module into MLIR as StableHLO, which is exactly the frontend IREE consumes:

```python
# export.py
import torch, torch_mlir

class Net(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.linear = torch.nn.Linear(8, 8)
    def forward(self, x):
        return torch.relu(self.linear(x))

module = torch_mlir.compile(Net().eval(), (torch.randn(4, 8),), output_type="stablehlo")
open("model.mlir", "w").write(str(module))
```

```bash
python export.py
iree-compile model.mlir --iree-hal-target-backends=llvm-cpu -o model.vmfb
iree-run-module --module=model.vmfb --function=main --input=4x8xf32=1,2,3
```

## TFLite path (via TOSA)

No PyTorch needed:

```bash
python -m pip install iree-tflite-tools
iree-import-tflite model.tflite -o model.mlir
iree-compile model.mlir --iree-hal-target-backends=llvm-cpu -o model.vmfb
iree-run-module --module=model.vmfb --function=main --input=...
```

The TFLite importer lowers into TOSA (the standard MLIR tensor-op dialect, see [tosa-stablehlo](../../MLIR/Dialects/tosa-stablehlo.md)).

## What each stage does

1. **Frontend**: `torch_mlir.compile` / `iree-import-tflite` turn the framework model into MLIR — StableHLO or TOSA. A graph of "softened" tensor ops, no code yet.
2. **Graph optimization (Flow dialect)**: IREE's Flow dialect does the global work — decides what becomes a **dispatch** (roughly one kernel launch), does shape/dependency analysis. This is where fusion happens.
3. **Scheduling (Stream dialect)**: ordering — what runs on which device, what serializes, resource lifetimes. Stream plans the execution timeline.
4. **HAL**: every dispatch is now a call into the HAL (`hal.command_buffer.dispatch`) — device-agnostic API calls. See [HAL dispatch](IREE-HAL-Dispatch.md).
5. **VM flatbuffer**: the whole thing is serialized into a `.vmfb` bytecode module — the program AND the scheduling baked in, so the runtime is a small VM that interprets it. That's why `iree-run-module` is a thin binary.

So `.vmfb` ≈ compiled kernels + a tiny program that drives them. Nothing is interpreted at the op level like eager PyTorch.

## Why it matters

This is the AOT loop I re-run for every "can IREE do X" question: model → `.mlir` → `iree-compile --iree-hal-target-backends=...` → `.vmfb` → `iree-run-module`. Swapping the backend flag is the entire retargeting story.

## Related

- [IREE](IREE.md) — overview and the dialect pipeline this note runs through.
- [HAL dispatch](IREE-HAL-Dispatch.md) — what `hal.command_buffer.dispatch` does at runtime.
- [Backends](IREE-Backends.md) — choosing `--iree-hal-target-backends`.
- [tosa-stablehlo](../../MLIR/Dialects/tosa-stablehlo.md) — the TOSA path.
