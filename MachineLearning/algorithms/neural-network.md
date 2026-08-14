# Neural network

There are various neural networks:
- Graph neural network
- Convolutional neural network
- Recurrent nerual network

## The common structure

Every neural net is a composition of parameterized transformations:

```
y = f_L(W_L · f_{L-1}(... f_1(W_1·x + b_1) ...) + b_L)
```

Each layer applies a linear map (`W·x + b`) followed by a non-linear activation. The parameters are learned by gradient descent on a [[loss-function]] (backpropagation). The non-linearity is essential — without it, stacked linear layers collapse into one linear map.

## The families (expanding the list above)

- **Feed-forward / MLP** — plain dense layers; universal approximator (see [[Universal-approximation-function]]).
- **Convolutional (CNN)** — weight sharing + locality via kernels; the standard for images (see [[MachineLearning/deeplearning/An-introduction-to-convolutional-neural-networks]]).
- **Recurrent (RNN/LSTM/GRU)** — hidden state carried across time steps; for sequences.
- **Graph neural networks (GNN)** — message passing between nodes; for graph-structured data.
- **Transformer** — self-attention; the modern default for sequences (see [[MachineLearning/deeplearning/Transformers]]).

## Training loop

1. Forward pass → predictions.
2. Compute [[loss-function|loss]] → [[cost-function|cost]].
3. Backward pass → gradients (chain rule).
4. Update weights: `w ← w - η·∇w` (SGD/Adam).

## Notes

- Depth, width, activation, and regularization choices matter more than "which family" once the data type is fixed.
- A neural net *is* an estimator — interchangeable with trees/linear models in the sklearn sense (see [[base-esitmator]]), just usually needing much more data.
