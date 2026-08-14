# Building a TensorRT engine from ONNX

The end-to-end: ONNX file → optimized engine → run inference. Two ways to build: `trtexec` (CLI) and the Python API. Both produce a `.engine` blob that is GPU-architecture-specific (see [TensorRT](TensorRT.md)).

## The API chain

Every build is the same 7 steps:

1. **Builder** (`builder`) — owns the build, produces engines.
2. **Network** (`network`) — the in-progress graph of layers.
3. **Parser** (`onnx_parser`) — reads the ONNX file, fills the network.
4. **BuilderConfig** (`config`) — precision, workspace, profiles, tactics.
5. `builder.build_serialized_network(network, config)` → **engine** bytes.
6. Deserialize → **runtime** → create an **execution context** (holds per-run state).
7. Set input buffers, `context.execute_v2(inputs, outputs)` → results.

## trtexec (fastest way to see it work)

```bash
trtexec --onnx=model.onnx --saveEngine=model.engine
trtexec --loadEngine=model.engine --inputShapes=input:1x3x224x224
```

Flags I actually use:

- `--fp16` / `--int8` — enable those precisions (INT8 also needs a calibrator; see [Precision calibration](TensorRT-Precision-Calibration.md)).
- `--workspace=4096` — max workspace (GB) the tactics may use.
- `--minShapes / --optShapes / --maxShapes` — dynamic-shape bounds (see [Optimization profiles](TensorRT-Optimization-Profiles.md)).
- `--verbose` — dumps which tactics/layers got chosen; my go-to for "why is this layer slow".
- `--saveEngine` — reuse the engine; never rebuild in production.

## Python API (the same thing, programmatically)

```python
import tensorrt as trt

logger = trt.Logger(trt.Logger.WARNING)
builder = trt.Builder(logger)
network = builder.create_network(1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH))
parser = trt.OnnxParser(network, logger)
with open("model.onnx", "rb") as f:
    parser.parse(f.read())                     # ONNX → network layers

config = builder.create_builder_config()
config.set_memory_pool_limit(trt.MemoryPoolType.WORKSPACE, 4 << 30)
config.set_flag(trt.BuilderFlag.FP16)          # optional precision

serialized = builder.build_serialized_network(network, config)   # → engine bytes
with open("model.engine", "wb") as f:
    f.write(serialized)
```

Running it (runtime API):

```python
runtime = trt.Runtime(logger)
engine = runtime.deserialize_cuda_engine(serialized)
context = engine.create_execution_context()
# bind buffers via cuda-python/pycuda, set input tensors
context.execute_v2(bindings)                   # async: execute_async_v2 + streams
```

## Precision flags

- Default: **FP32**.
- `--fp16` / `BuilderFlag.FP16`: run layers in FP16 where numerically safe — a free speedup, no calibration needed (FP16 is lossless-ish rounding).
- `--int8`: needs calibration (activation ranges per layer) — the real quantization story, see [Precision calibration](TensorRT-Precision-Calibration.md).
- TensorRT does **mixed precision** automatically: it only drops layers it proved safe.

## Why it matters

This is the 10-minute workflow that answers "does TensorRT make my model faster": export ONNX, trtexec, measure. The Python API is the same flow when I need it inside a training/validation loop. Engines are tied to GPU arch + TensorRT version — always rebuild on the real deployment machine.

## Related

- [TensorRT](TensorRT.md) — the pipeline overview this implements.
- [Precision calibration](TensorRT-Precision-Calibration.md) — the INT8 step.
- [Optimization profiles](TensorRT-Optimization-Profiles.md) — dynamic shapes.
- [TensorRT-LLM](TensorRT-LLM.md) — the LLM-specific builder.
