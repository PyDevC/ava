# Compilers — Plan

## Goal of this directory

Understand compilers end to end: from source/IR, through analyses and passes, down to target codegen — with a focus on **deep learning compilers** (which is where my career interest is). The notes should build a mental model of *how a compiler turns a model into fast machine code*.

## What's already here

- **DLCompilers/** — the big picture: IREE, TensorRT, DISC, TorchInductor, plus a README explaining the common pipeline (frontend → IR → lowering → tuning → runtime).
- **IR/SSA/** — the core IR theory: Dominators, PruneSSA.
- **MLIR/** — the framework: CMake guide, Affine dialect.
- **PyTorchCompiler/** — the PyTorch compile stack: Dynamo (graph breaks, debugging) and Inductor behavior.

## What to add next

- [x] **Core theory gaps** (before going deeper into DL):
  - [x] Liveness analysis + register allocation basics (SSA connects here — [Dominators](IR/SSA/Dominators.md))
  - [x] Loop transformations: tiling, fusion, unrolling, vectorization (feeds into [Affine](MLIR/Dialects/Affine.md))
  - [x] Dataflow analysis / reaching definitions / CSE
  - [x] SSA construction (the phi-placement algorithm that uses dominance frontiers) and phi elimination
  - [x] Static vs dynamic control flow in IRs; why graphs (FX) differ from CFGs
- [ ] **LLVM** (the engine under everything):
  - [ ] LLVM IR overview, passes, pass manager
  - [ ] LLVM backend pipeline: SelectionDAG/GlobalISel, instruction selection
  - [x] How LLVM is used by MLIR (`mlir-translate`, LLVM dialect)
- [x] **MLIR** depth:
  - [x] `scf` (structured control flow) and `vector` dialects — the counterparts to Affine
  - [x] DialectConversion / lowering framework (why passes exist)
  - [x] linalg dialect (the "softened op" IR that DL compilers love)
  - [x] The Toy tutorial walkthrough as a worked example
- [x] **More DL compilers** to compare against:
  - [x] TVM (the old classic — graph + tensor expression IR)
  - [x] XLA / StableHLO / JAX's compiler
  - [x] ONNX Runtime graph optimizations
- [ ] **Codegen specifics**:
  - [ ] Triton's approach (block-level kernels) — see also [triton-compiler-engineer](../Roadmaps/triton-compiler-engineer.md)
  - [ ] CUDA kernel launch, occupancy, why fusion matters
  - [ ] Reading the `.s`/PTX that Inductor actually emits

## Prioritization

1. Fill the **core theory gaps** (liveness, loops, SSA construction) — everything else references these.
2. Then **MLIR depth** — it's the practical tool for all the DL compilers I care about.
3. Then **more DL compilers + codegen** to round out comparisons.
