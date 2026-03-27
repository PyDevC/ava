---
id: auto-keras
aliases: []
tags: []
---

# Auto Keras

## Efficient architecture

Network morphism, which keeps the functionality of a neural network while 
changing its neural architecture, could be helpful for NAS by enabling more 
efficient training during the search.

## working
It has a predefined set of model architectures which have their own pre-defined 
weights.
The general neural architecture search problem we studied in this
paper is defined as: Given a neural architecture search space F , the
input data D divided into Dtrain and Dval , and the cost function
Cost(·), we aim at finding an optimal neural network f ∗ ∈ F ,
which could achieve the lowest cost on dataset D. The definition is
equivalent to finding f∗ satisfying.

f ∗ = argmin Cost(f (θ ∗), Dval ), (1)
       f ∈ F
θ ∗ = argmin L(f (θ ), Dtrain ). 
         θ


