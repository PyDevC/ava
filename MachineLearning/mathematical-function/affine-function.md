# Affine function

An affine function is a linear function **plus a constant (bias)**:

```
f(x) = a·x + b
```

- `a` = slope (coefficient), `b` = intercept (offset).
- If `b = 0`, it's a *linear* function (through the origin) — see [[linear-function]]. Every affine function is "linear + shift".

## In more dimensions

```
f(x) = W·x + b
```

`W` is a matrix, `b` a vector. This is exactly what a **linear/dense layer** in a neural network computes before the activation — see [[MachineLearning/algorithms/neural-network]].

## Why it matters in ML

- The building block of every linear model: regression `ŷ = w·x + b`, classification boundary.
- A **linear regression** is an affine model fit to data (see [[MachineLearning/algorithms/tabular-regression]], [[MachineLearning/algorithms/weighted-linear-regression]]).
- In a NN, stacking affine layers without non-linearities collapses to a single affine map — the activation is what makes networks non-linear (see the Universal approximation idea in [[MachineLearning/algorithms/Universal-approximation-function]]).
- The slope `a` is the *rate of change* — small `|a|` means output barely responds to input (cf. saturation in activations like the [[logistic-function]]).

## Properties

- Graph is a straight line (in 1D) / a hyperplane (in N-D).
- Affine maps preserve lines and parallelism (they're the transformations of "affine spaces").
