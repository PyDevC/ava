---
id: status
aliases: []
tags: []
---

```bash
$ sudo lshw | grep Radeon
$ product: Navi 32 [Radeon RX 7700 XT / 7800 XT]

pip install torch==2.4.1 torchvision==0.19.1 torchaudio==2.4.1 --index-url https://download.pytorch.org/whl/rocm6.1
python -c "import torch; print(torch.__version__)"
export HSA_OVERRIDE_GFX_VERSION=11.0.1
python -c "import torch; print(torch.cuda.is_available())"
$ False
export HSA_OVERRIDE_GFX_VERSION=11.0.0
python -c "import torch; print(torch.cuda.is_available())"
$ False
```
