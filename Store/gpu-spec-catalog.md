# GPU spec catalog

The reference for [[deep-learning-hardware]]: NVIDIA/AMD/Intel/cloud-chip generations and the numbers that matter (tensor-core availability, memory, interconnect). Numbers drift with SKUs — this is the *shape* of the catalog, keep the precise clocks current.

## NVIDIA (Volta → Blackwell)

| Arch | Gen | Tensor cores | Memory / bandwidth | Interconnect |
|---|---|---|---|---|
| **Volta** (2017) | V100 | FP16 tensor cores (first gen) | 16–32GB HBM2, ~900GB/s | NVLink 2, ~150GB/s |
| **Turing** (2018) | T4/RTX | FP16 + INT8 (real-time ray tracing) | GDDR6 | NVLink (limited) |
| **Ampere** (2020) | A100 | FP16/BF16/INT8/TF32 | 40–80GB HBM2e, ~2TB/s | NVLink 3 (~600GB/s) |
| **Hopper** (2022) | H100 | + FP8 tensor cores, transformer engine | 80GB HBM3, ~3.35TB/s | NVLink 4, ~900GB/s; NVSwitch |
| **Blackwell** (2024+) | B200/GB200 | FP4/FP8, 2-die | 192GB HBM3e, ~8TB/s | NVLink 5, NVLink-C2C |

The story: tensor cores appeared at Volta; every gen since adds precision (FP16→BF16→FP8→FP4) and memory bandwidth. **Bandwidth > compute** for most LLM inference (see the roofline note) — that's why HBM size/speed dominate the specs.

## AMD (CDNA) and Intel

- **CDNA2** (MI250, 2021): BF16/FP16, HBM2e, ~1.6TB/s; **CDNA3** (MI300X, 2023): 192GB HBM3, ~5.3TB/s — the *largest* memory of the era (the reason LLM people started buying AMD). ROCm is the CUDA equivalent (see [[Roadmaps/rocm-developers]]).
- **RDNA** (gaming line): no serious tensor-core story for training; the AI story is CDNA.
- **Intel Gaudi** (Habana): memory-bandwidth-priced ML accelerators (2×/3×); **Gaudi 2/3** target LLM training/inference with onboard HBM. Intel Arc/iGPU have XMX units (DP4a/XMX) — used for lightweight on-device inference (see [[Compilers/DLCompilers/OpenVINO-TFLite]]).

## Cloud custom chips

- **Google TPU**: v4 (2021, 4096-chip pods, ~10^17 FLOPs/pod), v5e/v5p (2023), v6 (Trillium). Interconnect = the *pod* (a supercomputer-class network, not PCIe/NVLink) — the "scale-out by design" architecture (XLA is its compiler, [[Compilers/DLCompilers/XLA-StableHLO]]).
- **AWS**: Inferentia (inference, Neuron SDK), Trainium (training). Custom interconnect (EFS, NeuronLink).
- **Meta** (MTIA), **Microsoft** (Maia) — in-house inference chips; the "chip per hyperscaler" trend.

## The reference facts to remember

- **Tensor cores** = the matmul engines; mixed-precision is the whole game ([[MachineLearning/deeplearning/mixed-precision]]).
- **NVLink/NVSwitch** = the intra-node/rack fabric; InfiniBand/Ethernet = the inter-node. Distributed training ([[MachineLearning/deeplearning/distributed-training]]) lives and dies by these numbers.
- **Roofline**: memory-bound vs compute-bound. A 1.7B-param model at FP8 is ~3.4GB of weights — at 3TB/s that's ~1.1ms *just reading the weights* per token. Bandwidth is the LLM-inference constraint (see the roofline note).

## Related

- [[deep-learning-hardware]] — the concepts behind these numbers.
- [[Roadmaps/gpu-architect]] — the role this feeds.
- [[MachineLearning/deeplearning/mixed-precision]] — what the tensor cores compute.
- [[Roadmaps/rocm-developers]] — the AMD stack.
