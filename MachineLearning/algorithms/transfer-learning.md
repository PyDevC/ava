# Transfer learning

Transfer learning = taking knowledge learned on **one task** (usually a big, generic dataset) and reusing it to do **better and faster on a related task** with much less data. Instead of training from scratch, you start from a pretrained model.

## The core idea

Models learn features in stages: early layers learn generic features (edges, textures, low-level patterns), later layers learn task-specific ones. So:

- Freeze (or lightly fine-tune) the early layers → they're reusable.
- Replace / retrain the head (the last layers) → task-specific output.
- Fine-tune everything with a small learning rate if data allows.

## When it helps

- **Little data**: your target dataset is small; a pretrained backbone (ImageNet CNN, BERT, CLIP, Whisper) gives features you could never learn from few examples.
- **Expensive training**: saving compute/time.
- **Domain shift is mild**: the source and target are related (image→image, text→text).

## Common patterns

- **Feature extraction**: freeze backbone, train only the head (fast, cheap).
- **Fine-tuning**: unfreeze and train everything with a low LR (better but needs more data/care).
- **Progressive unfreezing**: gradually unfreeze layers from the top down.
- **Domain adaptation**: a form of transfer where source ≠ target distribution (different camera, different style).

## Related

- Pretraining is the extreme version of this — see the Transformer/BERT story in [[MachineLearning/deeplearning/Transformers]] and [[MachineLearning/nlp/BERT]].
- [[MachineLearning/automl/meta-learning]] and [[MachineLearning/automl/few-shot-learning]] are close cousins (learning-to-learn, and using very few examples).
- In AutoML ([[MachineLearning/automl/auto-sklearn]]) transfer can seed warm-starting models.
