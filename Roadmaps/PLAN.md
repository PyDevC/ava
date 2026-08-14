# Roadmaps — Plan

## Goal of this directory

A **career map** for the roles I'm interested in (compilers, frameworks, ML, hardware): for each role, the core skills, what a typical day looks like, and a roadmap to get there. The goal is to see how my current knowledge (the rest of this repo) maps onto these roles — and what's missing.

## What's already here

- ML-side: [ml-engineer](ml-engineer.md), [ai-engineer](ai-engineer.md), [research-scientist](research-scientist.md), [data-analyst](data-analyst.md)
- Compiler/systems-side: [ai-compiler-engineer](ai-compiler-engineer.md), [ai-framework-engineer](ai-framework-engineer.md), [triton-compiler-engineer](triton-compiler-engineer.md), [pytorch-developers](pytorch-developers.md)
- Hardware/systems-side: [gpu-architect](gpu-architect.md), [rocm-developers](rocm-developers.md), [hpc-engineer](hpc-engineer.md), [devops-engineer](devops-engineer.md)
- Modern additions: [mlops-engineer](mlops-engineer.md), [backend-infra-engineer](backend-infra-engineer.md), [model-optimization-engineer](model-optimization-engineer.md), [llm-engineer](llm-engineer.md)
- **[where-am-i-now](where-am-i-now.md)** — the "which boxes are already ticked" mapping (the real payoff).

## What to add next

- [x] **Roles that are implied but not written**:
  - [x] MLOps engineer (the "deployment half" that [ml-engineer](ml-engineer.md) touches)
  - [x] Backend/infra engineer (the general-SWE baseline underneath everything)
  - [x] Model optimization engineer (quantization/pruning/distillation specialist — spans ML and compilers)
  - [x] NLP engineer / LLM engineer (modern and in-demand; builds on [nlp](../MachineLearning/nlp/PLAN.md))
- [x] **Cross-cutting additions**:
  - [x] A **"where I am now"** mapping note — which boxes in each roadmap my current notes already tick (e.g. compilers ✓, GPU hardware ✗). This is the real payoff.
  - [ ] Skills matrix: which repo directories feed each role (link-heavy index note) → largely covered by [where-am-i-now](where-am-i-now.md)
  - [ ] Interview prep notes per role (systems design, ML design, coding)
- [ ] **Depth per existing role** (as I learn):
  - [ ] [ai-compiler-engineer](ai-compiler-engineer.md) — add a concrete "project ladder" (build a dialect → write a pass → contribute to IREE/Inductor)
  - [ ] [pytorch-developers](pytorch-developers.md) — map to actual PR-sized tasks (see [PLAN](../PyTorch/PLAN.md))
  - [ ] [rocm-developers](rocm-developers.md) — add hands-on project ideas (write HIP kernels, benchmark vs CUDA)

## Prioritization

1. **"Where I am now" mapping** — turns the roadmaps from info into an action plan.
2. **MLOps + LLM engineer** — the two most relevant modern additions.
3. **Project ladders** per compiler role — concrete next steps tied to the rest of the repo.
