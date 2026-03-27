---
id: tpot
aliases: []
tags: []
---

# TPOT (Tree-based learning pipeline optimization tool)

It uses genetic programming that optimizes a series of feature preprocessors 
and machine learning models with the goal of maximizing classification accuracy
on a supervised classification task.

## operators
It has three major operators for performing different tasks:

- Supervised classification operators: has various machine learning classifiers,
operators store predictions of a classifiers as new feature and as well as for 
classification pipeline.

- Feature preprocessor operators: these are mostly scalers and normalizers. Used for data cleansing.

- Feature selection operators: reduce the number of features in the data set 
using some criteria and return the modified data set. VarianceThreshold, 
SelectKBest, SelectPercentile, SelectFwe, and Recursive Feature Elimination (RFE).

All they do is create samplings (generations) and each generation can mutate,
this approach makes it getting closer to the actual optimal algorithm.
