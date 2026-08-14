# Store — Plan

## Goal of this directory

A **catch-all / reference** directory for material that doesn't fit the structured notes: hardware references, curated links, saved resources, and pointers for future reading. Less "explain the concept" (that's [[../MachineLearning]] and [[../Compilers]]) and more "store the reference so I can find it".

## What's already here

- [[deep-learning-hardware]] — the hardware landscape: GPUs (tensor cores), dedicated accelerators (TPU, Inferentia, NNP), and how they execute DL models.
- [[gpu-spec-catalog]] — NVIDIA/AMD/Intel/cloud-chip generations, memory, interconnect.
- [[roofline-model]] — bandwidth vs compute, why LLM inference is memory-bound.
- [[paper-library]] — read + to-read papers with links into the notes.
- [[glossary]] — the acronym index into the real notes.

## What to add next

- [x] **Hardware references** (extends the one existing note):
  - [x] A GPU spec catalog: NVIDIA (Volta→Hopper/Blackwell) — tensor core generations, memory, NVLink
  - [x] AMD (CDNA/RDNA) + Intel + cloud custom chips (TPU v4+, Inferentia, Trainium)
  - [x] Why bandwidth > compute for many workloads (roofline mental model)
- [x] **Paper / resource library**:
  - [x] The papers I've read or want to read (with one-line summaries and links) — starts with [[../MachineLearning/deeplearning/attention-is-all-you-need]]
  - [ ] Conference/reading-list pointers (NeurIPS/ICML/ICLR, ISCA/MICRO/HotChips for hardware) → started in [[paper-library]]
- [ ] **Bookmarks / tooling**:
  - [ ] Tools worth installing (profilers, compilers, autotuners) — links only
  - [ ] Repos worth studying (PyTorch, Triton, IREE, MLIR) — see [[../OpenSource/how-to-explore-large-repos]]
- [x] **Naming / vocabulary reference** — the acronyms that keep appearing (XLA, HAL, HSA, DDP, FSDP...) — a glossary index linking into the real notes.

## Prioritization

1. **Paper library** — highest value-to-effort for keeping my reading organized.
2. **GPU spec catalog** — completes [[deep-learning-hardware]] and supports [[../Roadmaps/gpu-architect]].
3. **Glossary index** — cheap, and turns the whole repo into a usable reference.
