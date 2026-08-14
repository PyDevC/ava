# Paper and resource library

The "to read / read" shelf — one-line summaries with links into the notes. The list grows as I read; each entry should be *searchable* and *linked*, not a wall of text.

## Read (with notes in this KB)

- **Attention Is All You Need** (Vaswani et al., 2017) — the Transformer paper: self-attention, multi-head, positional encodings. → [attention-is-all-you-need](../MachineLearning/deeplearning/attention-is-all-you-need.md)
- **BERT** (Devlin et al., 2018) — masked-LM pretraining for bidirectional encoders. → [BERT](../MachineLearning/nlp/BERT.md)
- **T5** (Raffel et al., 2020) — everything-is-text-to-text. → [T5-encoder-decoder](../MachineLearning/nlp/T5-encoder-decoder.md)
- **Adam** (Kingma & Ba, 2014) — the optimizer everyone uses. → [model-optimization](../MachineLearning/deeplearning/model-optimization.md)
- **U-Net** (Ronneberger et al., 2015) — encoder-decoder with skip connections for segmentation. → [segmentation](../MachineLearning/computer-vision/segmentation.md)
- **YOLO** (Redmon et al., 2016) — one-stage detection. → [object-detection](../MachineLearning/computer-vision/object-detection.md)
- **Prototypical Networks** (Snell et al., 2017) — metric-based few-shot. → [prototypical-matching-networks](../MachineLearning/automl/prototypical-matching-networks.md)
- **AdamW** (Loshchilov & Hutter, 2017) — decoupled weight decay. → [regularization-normalization](../MachineLearning/deeplearning/regularization-normalization.md)
- **GANs** (Goodfellow et al., 2014) — the adversarial training game. → [GANs](../MachineLearning/deeplearning/GANs.md)
- **DDPM** (Ho et al., 2020) — diffusion as denoising. → [diffusion-models](../MachineLearning/deeplearning/diffusion-models.md)
- **AlphaFold / CNN lineage** (context) — deep learning for structured prediction. → [hestreg-model](../MachineLearning/computer-vision/hestreg-model.md)

## To read (want-to-list, one-liners)

- **ResNet** (He et al., 2015) — residual connections, the deep-net unlock.
- **RoPE** (Su et al., 2021) — rotary position embeddings (see [positional-encodings](../MachineLearning/nlp/positional-encodings.md)).
- **LoRA** (Hu et al., 2021) — low-rank adaptation for fine-tuning.
- **HNSW** (Malkov & Yashunin, 2016) — the ANN index behind vector search.
- **FlashAttention** (Dao et al., 2022) — the memory-efficient attention kernel.
- **Ansor** (Zheng et al., 2020) — schedule search for DL compilers (see [TVM](../Compilers/DLCompilers/TVM.md)).
- **Scaling Laws** (Kaplan et al., 2020 / Chinchilla 2022) — the compute-data-model curves.

## Reading routes

- **ML theory/applications**: NeurIPS, ICML, ICLR proceedings; the arXiv cs.LG/CL categories.
- **Compiler/systems**: ISCA, MICRO, ASPLOS, HotChips (hardware); the MLIR/LVM community talks.
- **The "follow the repo" route**: read the references cited by the papers in this KB — most link straight back to the notes above.

## Related

- [attention-is-all-you-need](../MachineLearning/deeplearning/attention-is-all-you-need.md) — the flagship note.
- [how-to-explore-large-repos](../OpenSource/how-to-explore-large-repos.md) — reading a codebase the way you read a paper.
- [PLAN](PLAN.md) — this library's home directory.
