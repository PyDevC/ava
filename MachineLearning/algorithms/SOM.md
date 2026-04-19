# Seft-organizing maps

Self organizing maps is the unsupervised machine learning technique use to map high dimensional dataset to two dimensions using the similarity in the values, clustering them together in fixed hexagon like shapes.
It does all this without losing any structural topology. Using SOM we can visualize and analyze the higher dimensional data on a plane.

Instead of using Graident decent and backpropagation it uses competitive learning (A form of learning where nodes compete for the right to respond to a input of data).

It works in two modes: Training and mapping
Training is the phase where map adjusts itself according the data, for some nodes (neuron, hexagon) with p variables (p dimensions) it maps to two dimensional plane, the number of nodes and their arragement is predetermined.
In Mapping mode we get the mapped value of a subset of input data.

Each node in map space is associated with a weighted vector which is to be adjusted towards the input data without losing it's topological structure. After training, map can be used to classify the additonal observations from input data by finding the node closest to the weighted vector.

For more info check [https://mlab.taik.fi/~timo/som/thesis-som.html](https://mlab.taik.fi/~timo/som/thesis-som.html).
