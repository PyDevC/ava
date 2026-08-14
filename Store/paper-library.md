# Paper and resource library

The "to read / read" shelf — one-line summaries with links into the notes. The list grows as I read; each entry should be *searchable* and *linked*, not a wall of text.

## Read (with notes in this KB)

- **Attention Is All You Need** (Vaswani et al., 2017) — the Transformer paper: self-attention, multi-head, positional encodings. → [[MachineLearning/deeplearning/attention-is-all-you-need]]
- **BERT** (Devlin et al., 2018) — masked-LM pretraining for bidirectional encoders. → [[MachineLearning/nlp/BERT]]
- **T5** (Raffel et al., 2020) — everything-is-text-to-text. → [[MachineLearning/nlp/T5-encoder-decoder]]
- **Adam** (Kingma & Ba, 2014) — the optimizer everyone uses. → [[MachineLearning/deeplearning/model-optimization]]
- **U-Net** (Ronneberger et al., 2015) — encoder-decoder with skip connections for segmentation. → [[MachineLearning/computer-vision/segmentation]]
- **YOLO** (Redmon et al., 2016) — one-stage detection. → [[MachineLearning/computer-vision/object-detection]]
- **Prototypical Networks** (Snell et al., 2017) — metric-based few-shot. → [[MachineLearning/automl/prototypical-matching-networks]]
- **AdamW** (Loshchilov & Hutter, 2017) — decoupled weight decay. → [[MachineLearning/deeplearning/regularization-normalization]]
- **GANs** (Goodfellow et al., 2014) — the adversarial training game. → [[MachineLearning/deeplearning/GANs]]
- **DDPM** (Ho et al., 2020) — diffusion as denoising. → [[MachineLearning/deeplearning/diffusion-models]]
- **AlphaFold / CNN lineage** (context) — deep learning for structured prediction. → [[MachineLearning/computer-vision/hestreg-model]]

## To read (want-to-list, one-liners)

- **ResNet** (He et al., 2015) — residual connections, the deep-net unlock.
- **RoPE** (Su et al., 2021) — rotary position embeddings (see [[MachineLearning/nlp/positional-encodings]]).
- **LoRA** (Hu et al., 2021) — low-rank adaptation for fine-tuning.
- **HNSW** (Malkov & Yashunin, 2016) — the ANN index behind vector search.
- **FlashAttention** (Dao et al., 2022) — the memory-efficient attention kernel.
- **Ansor** (Zheng et al., 2020) — schedule search for DL compilers (see [[Compilers/DLCompilers/TVM]]).
- **Scaling Laws** (Kaplan et al., 2020 / Chinchilla 2022) — the compute-data-model curves.

## Reading routes

- **ML theory/applications**: NeurIPS, ICML, ICLR proceedings; the arXiv cs.LG/CL categories.
- **Compiler/systems**: ISCA, MICRO, ASPLOS, HotChips (hardware); the MLIR/LVM community talks.
- **The "follow the repo" route**: read the references cited by the papers in this KB — most link straight back to the notes above.

## Related

- [[MachineLearning/deeplearning/attention-is-all-you-need]] — the flagship note.
- [[OpenSource/how-to-explore-large-repos]] — reading a codebase the way you read a paper.
- [[../Store/PLAN]] — this library's home directory.
