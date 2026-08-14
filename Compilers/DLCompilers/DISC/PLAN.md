# DISC — Plan

## Goal

Note on **BladeDISC** (Alibaba's deep learning compiler): what it optimizes (graph-level + kernel-level), how it handles the XLA heritage, and dynamic shapes.

## What's here

- [[DISC]] — overview: open-sourcing, XLA/static-shape history, paper arXiv:2103.05288.

## What to add next (see also [[../PLAN]])

- [ ] The graph/kernel two-level design in detail
- [ ] Dynamic-shape handling mechanics
- [ ] A comparison against IREE/TorchInductor on the same model
- [ ] Where it fits in Alibaba's serving stack (interference, MLU/GPU targets)
