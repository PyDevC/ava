# Roadmaps — Plan

## Goal of this directory

A **career map** for the roles I'm interested in (compilers, frameworks, ML, hardware): for each role, the core skills, what a typical day looks like, and a roadmap to get there. The goal is to see how my current knowledge (the rest of this repo) maps onto these roles — and what's missing.

## What's already here

- ML-side: [[ml-engineer]], [[ai-engineer]], [[research-scientist]], [[data-analyst]]
- Compiler/systems-side: [[ai-compiler-engineer]], [[ai-framework-engineer]], [[triton-compiler-engineer]], [[pytorch-developers]]
- Hardware/systems-side: [[gpu-architect]], [[rocm-developers]], [[hpc-engineer]], [[devops-engineer]]

## What to add next

- [ ] **Roles that are implied but not written**:
  - [ ] MLOps engineer (the "deployment half" that [[ml-engineer]] touches)
  - [ ] Backend/infra engineer (the general-SWE baseline underneath everything)
  - [ ] Model optimization engineer (quantization/pruning/distillation specialist — spans ML and compilers)
  - [ ] NLP engineer / LLM engineer (modern and in-demand; builds on [[../MachineLearning/nlp]])
- [ ] **Cross-cutting additions**:
  - [ ] A **"where I am now"** mapping note — which boxes in each roadmap my current notes already tick (e.g. compilers ✓, GPU hardware ✗). This is the real payoff.
  - [ ] Skills matrix: which repo directories feed each role (link-heavy index note)
  - [ ] Interview prep notes per role (systems design, ML design, coding)
- [ ] **Depth per existing role** (as I learn):
  - [ ] [[ai-compiler-engineer]] — add a concrete "project ladder" (build a dialect → write a pass → contribute to IREE/Inductor)
  - [ ] [[pytorch-developers]] — map to actual PR-sized tasks (see [[../PyTorch/PLAN]])
  - [ ] [[rocm-developers]] — add hands-on project ideas (write HIP kernels, benchmark vs CUDA)

## Prioritization

1. **"Where I am now" mapping** — turns the roadmaps from info into an action plan.
2. **MLOps + LLM engineer** — the two most relevant modern additions.
3. **Project ladders** per compiler role — concrete next steps tied to the rest of the repo.
