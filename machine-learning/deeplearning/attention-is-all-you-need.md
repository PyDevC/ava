# Attention is all you need

This is short review of the research paper "Attention is all you need". I have added all the points you need to know about the Attention mechanism and some parts of Transformers, though you should read about Transformers [from here](./Transformers.md).

## Need for Attention based models
Previous Recurrent and convolutional models were very computationally expensive as well as didn't had much parallizable capabitlity as that of a Transformer. There were very few kind of models that pioneered in the field of variable length sequence inputs required to train a deeplearning model. With Large training sets the memory became a bottleneck as each layer is required to be stored before you can even handle the weights of further computation. While Attention mechanisms were coming out around this time they were still not considered as good as Recurrent neural nets due as they can't be used alone without relying on other parts of neural model.

The paper mentions some models such as Extended Neural GPU, ByteNet, ConvS2S which uses convolutional as basic blocks of their model. In these models the number of operations grows really quickly (linearly for ConvS2S and logorithmically for ByteNet).

## Model Architecture
The model follows a Encoder-decoder structure which consists of a Encoder Stack and Decoder Stack combined together.
The encoder maps the symbol representation (words) to a sequence of continuous representations (vector). The Decoder, given this continuous representations then generates the output of symbol (word) on at a time. We call this auto-regressive model, as output of previous inference of model produced a new word which is then fed as input combined with previous input to the next inference.

Encoder Stack: The encoder is composed of N = 6 layers where each layer has two sublayers, one is multi-head self attention layer and another is position-wise feed forward neural network. Output of each layer needs to be normalized using Layer Normalization function LayerNorm(x + SubLayer(x)). Now the question is Why normalize after each sublayer, each sublayer performs a set of deep complex operations making it easy for Gradients to vanish under it's own weight and complexity of the model, also the without these Normalizations the model's weight will increase exponentially. Where as Add (x + SubLayer(x)) prevent model from learing everything from scratch after each layer so the weights will flow.
Decoder Stack: The decoder is composed of N = 6 layers where each layer with two sublayers same as encoder, decoder inserts a third sublayer which performs multi-head attention over the output of encoder stack. It also modifies the self-attention layer in the decoder stack to prevent the attention from catching the subsequent positions. This masking (called masked multi-head attention), combined with the fact that each output embedding is offset by one ensures that the predictiosn from previous i can depend on known output of i - 1 positions.

## What is Attention
Attention is a function that maps a query (input sequence), key value pairs to an output. The output is computed as weighted sum of the values, where the weight assinged to each value is computed by a compatibitlity function of the query with the corresponding key.

### Scalar Dot-Product Attention
This is softmax based matmul type of attention where key, value, query, output all are vectors. The input consists of query and keys of dimension d_k, and the values of dimension d_v (Note that the query vector is of variable length). It compute the dot products of the query with all keys, divide each by √d_k, and apply a softmax function to obtain the weights on the values. 

> NOTE: You need to look the formula of Attention in the paper itself, it is really easy you will know what is happening.

There are two different attentions, Add Attention and Dot-Product Attention out of which Dot-Product Attention is much faster than the other because it can be easily parallelized.

### Multi-head Attention
Instead of performing a single attention function on the input and key-value pair, we try to project them together linearly h times with different learned projections to d_k from d_k and d_v. The Matrix Generated performs the Attention Function in parallel yielding d_v dimension output values. The outputs are concatenated and once again projected, resulting in the final values.

> NOTE: You need to look the formula of Multi-head Attention in the paper itself.

## Perks of using Attention
Attention mimics the typical Encoder-decoder model making it easy to map to other sequence-to-sequence model. It retains the memory of larger sequences effectively without requiring massive computation units.

## End Note

The other parts of model is relatively easy you can learn that on your own from the paper. Make sure you study why we use Feed-forward and Embeddings as well as Softmax in the model.

#### Learn More

The code is available on - [https://github.com/tensorflow/tensor2tensor](https://github.com/tensorflow/tensor2tensor)
Link to Paper - [https://arxiv.org/pdf/1706.03762](https://arxiv.org/pdf/1706.03762)
