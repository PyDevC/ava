---
id: jet-mathematical-modeling
aliases: []
tags: []
---

Prompt1:
I want to create a machine learning technique with this description.
we call it JET.
JET should be able to handle raw datasets such as images, text,and structred dataset.
Let us assume that each dataset belongs to a class such as linear model, manifold, etc.
Plus a dataset can have more than 1 class.
Our goal is to find the best class for training this model.

Answer:

Key features of JET:
- multi-modal: JET should be able to handle multi-modal dataset.
- It will preprocess these different data types appropriately to transform them into a common representation or structure for model selection.
- dataset can belong to different model classes.
- It will choose between different dataset based on these characteristics
  - Data type (e.g., image vs. text)
  - Dimensionality (e.g., high-dimensional vs. low-dimensional data)
  - Data distribution (e.g., Gaussian, non-Gaussian, sparse)
  - Complexity (e.g., linear vs. non-linear patterns)
- Iterative learning: use meta learning to check which approach works the best for the system.

Prompt2:
So I guess we should use meta learning for selecting correct models.
Let us assume that the meta data required for meta learning is feature selected from the dataset.
We rank models in a leaderboard and new leaderboard is generated out of top 3.
and 2 random models.The new leaderboard generation starts as soon as the learning becomes slow.
When learning accuracy reaches above 90% then the first model is given out.
