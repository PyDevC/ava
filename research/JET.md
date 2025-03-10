---
id: JET
aliases: []
tags: []
---

# JET

JET is Automl tool which is part of infoto AI.

The kind of inputs JET can take:
- text
- images
- pre-trained models
- structured data

## model

JET will use the end model to predict the model required for the dataset image 
This is the ChatGPT approach, This is the thing no one else is doing in AUTOML.

## Aummptions in datasets
- The dataset will be turned into image for classification of the model. But with our current approach it is hard to keep up with this assumption. (most probably it is not needed)
- A pre-trained model will use for training end model for the dataset using transfer learning.

## Test v1 

### Failure
- Failed due to not using metaclassifier to train the model. 
- [x] not having enough data-insights
- poor categorical data management. (not exactly a failure point)
- [x] poor normalization 
- overfitting too much that it only fitted for one class.

## Test v2 New approach

### Key Takes
- we are using a multi class approach to classify the dataset at different degree or level. Ex: D1 is dataset good with random forest classifier so the output will be ensemble and then random forest classifier.
- We extracting important features from a dataset.
- Model will be trained on multiple datasets that have different meta data such as dimensionality, etc.

### working
- create a dataset pool, Say DH.
- select a dataset x from DH.
- extract features from dataset x using various tech.
- extract metadata from the dataset x.
- There will be class levels.
- There will be three instances of model when training

### class levels 
The class or labels of dataset optimal algorithm will be hierarchical.<br>
Classifier           Regression       Clustering<br>
    |                     |               |<br>
ensemble                linear         cluster<br>
    |                     |               |<br>
random-forest        linearregression   kmeans<br>

### Areas that need Work 
