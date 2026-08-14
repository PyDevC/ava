# Compilers — Plan

## Goal of this directory

Understand compilers end to end: from source/IR, through analyses and passes, down to target codegen — with a focus on **deep learning compilers** (which is where my career interest is). The notes should build a mental model of *how a compiler turns a model into fast machine code*.

## What's already here

- **DLCompilers/** — the big picture: IREE, TensorRT, DISC, TorchInductor, plus a README explaining the common pipeline (frontend → IR → lowering → tuning → runtime).
- **IR/SSA/** — the core IR theory: Dominators, PruneSSA.
- **MLIR/** — the framework: CMake guide, Affine dialect.
- **PyTorchCompiler/** — the PyTorch compile stack: Dynamo (graph breaks, debugging) and Inductor behavior.

## What to add next

- [ ] **Core theory gaps** (before going deeper into DL):
  - [ ] Liveness analysis + register allocation basics (SSA connects here — [[IR/SSA/Dominators]])
  - [ ] Loop transformations: tiling, fusion, unrolling, vectorization (feeds into [[MLIR/Dialects/Affine]])
  - [ ] Dataflow analysis / reaching definitions / CSE
  - [ ] SSA construction (the phi-placement algorithm that uses dominance frontiers) and phi elimination
  - [ ] Static vs dynamic control flow in IRs; why graphs (FX) differ from CFGs
- [ ] **LLVM** (the engine under everything):
  - [ ] LLVM IR overview, passes, pass manager
  - [ ] LLVM backend pipeline: SelectionDAG/GlobalISel, instruction selection
  - [ ] How LLVM is used by MLIR (`mlir-translate`, LLVM dialect)
- [ ] **MLIR** depth:
  - [ ] `scf` (structured control flow) and `vector` dialects — the counterparts to Affine
  - [ ] DialectConversion / lowering framework (why passes exist)
  - [ ] linalg dialect (the "softened op" IR that DL compilers love)
  - [ ] The Toy tutorial walkthrough as a worked example
- [ ] **More DL compilers** to compare against:
  - [ ] TVM (the old classic — graph + tensor expression IR)
  - [ ] XLA / StableHLO / JAX's compiler
  - [ ] ONNX Runtime graph optimizations
- [ ] **Codegen specifics**:
  - [ ] Triton's approach (block-level kernels) — see also [[../Roadmaps/triton-compiler-engineer]]
  - [ ] CUDA kernel launch, occupancy, why fusion matters
  - [ ] Reading the `.s`/PTX that Inductor actually emits

## Prioritization

1. Fill the **core theory gaps** (liveness, loops, SSA construction) — everything else references these.
2. Then **MLIR depth** — it's the practical tool for all the DL compilers I care about.
3. Then **more DL compilers + codegen** to round out comparisons.
