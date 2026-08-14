# Classification

Classification is a supervised learning task where the target `y` is a **discrete label** (class). The model learns a decision boundary to assign new inputs to one of the classes.

## Types

- **Binary**: two classes (spam / not-spam). Output is a probability `P(class=1 | x)`.
- **Multiclass**: more than two classes (digit 0–9). Output is a probability vector, typically from a softmax (see [[MachineLearning/mathematical-function/softmax-function]]).
- **Multi-label**: an input can belong to several classes at once (tags on a photo) — separate binary problems per label.

## Common algorithms

- Logistic regression (binary: sigmoid, multiclass: softmax) — see [[MachineLearning/mathematical-function/logistic-function]].
- Decision trees / random forests / boosting — see [[descision-trees]], [[Gradient-boosting]].
- SVMs (separating hyperplane with max margin).
- k-NN (label by majority vote of neighbors).
- Neural networks (softmax output + cross-entropy loss).

## Evaluation

Not accuracy alone — use **precision, recall, F1, ROC-AUC** especially for imbalanced data. Confusion matrix shows where misclassifications happen (false positives vs false negatives), which often have very different costs.

## Connection to other notes

- The loss used is usually the cross-entropy / log loss — see [[loss-function]] and [[cost-function]].
- A classifier is a particular case of a [[neural-network]] when the output layer uses softmax.
- If the boundary maps a score to a probability, that mapping is the [[MachineLearning/mathematical-function/logit-function]] (inverse of logistic).
