# An Introduction to Convolutional Neural Network

CNNs are designed to reduce the number of weights and neurons in the networks by adding some feature filters using Convolutional layers. Each Convolution uses a Kernel do decide the size of filter window called Convolution Kernel, this kernel calculates the scalar product of input and outputs a scalar representing the features of the kernel. In Convolution layer we indeed lose some amount of information but in case of image recognition where the a group of pixels shows the features instead of individual pixels and in most cases the surrrounding pixels are mostly same.

After Convolutional layers we have some more pooling layers and then a forward feed NN which does all the work same as ANN.

## What is a Convolutional Layer
A Convolutional layer convolve it's input and pass it to next layer that is pooling layer.

What does convolve means? 

Convolution slides a small window (the **kernel**) over the input and, at each position, computes the dot product between the kernel weights and the patch under the window. The result is a **feature map**. Each kernel learns to detect one local pattern (edge, corner, texture).

Key properties that make CNNs efficient:

- **Weight sharing**: one kernel is reused across the whole image → *way* fewer parameters than a fully connected layer (the whole point — see intro above).
- **Local connectivity**: each output only sees a small neighborhood → matches the fact that nearby pixels are correlated and the surrounding pixels are mostly the same.
- **Translation equivariance**: a pattern is detected wherever it appears.

### Hyperparameters of a convolution

- **Kernel size** — window size (e.g. 3×3).
- **Stride** — step between positions (stride 2 downsamples).
- **Padding** — keep border pixels / preserve spatial size.

## What is a Pooling Layer

Pooling downsamples each feature map by taking a statistic over a window (max pooling = max, average pooling = mean). It:

- reduces the spatial size → fewer params, less compute;
- gives a small amount of translation invariance;
- is the "information-losing" step (see intro note) — we trade spatial detail for robustness.

## The overall architecture

```
Input → [Conv + ReLU (+ pooling)] × N → Flatten → Fully-connected (ANN) → Softmax output
```

The conv part is a learned **feature extractor** (see [[MachineLearning/automl/automated-feature-extraction]]), the FC part is the classifier on top ([[MachineLearning/algorithms/classification]]).

## Notes

- Later convs learn higher-level features (faces → objects), early ones low-level (edges).
- Modern vision models still start from this stack, but often replace FC heads with global pooling and use attention (ViT) for the encoder part.
