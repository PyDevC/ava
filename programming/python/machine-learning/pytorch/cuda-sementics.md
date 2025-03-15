---
id: cuda-sementics
aliases: []
tags: []
---

torch.cuda is used to setup and run cuda operations. It keeps track of the current
selected GPU and load all tensors on that device.

Cross-GPU operations are not allowed unless you enable peer-to-peer memory access,
any attempts on launching operations on tensor loaded on different devices will raise error.

## Allocation of tensor
A tensor can be directly loaded on a GPU or can be transferred from CPU to GPU.

