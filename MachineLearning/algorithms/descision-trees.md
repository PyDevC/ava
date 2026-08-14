# Decision trees

Decision trees are supervised models that split the feature space recursively into regions, learning a piecewise-constant function. Each internal node is a test on one feature (`x_i > t`), each leaf holds a prediction (a class or a constant value).

## How they're built (CART)

1. Start with the whole dataset at the root.
2. At each node, pick the feature + threshold that **best splits** the data, by some impurity measure:
   - **Gini impurity** or **cross-entropy** for classification.
   - **Variance reduction (MSE)** for regression.
3. Recurse on each child until a stopping criterion (max depth, min samples per leaf, pure node).

## Why we use impurity, not accuracy

Splitting on accuracy doesn't directly reduce the loss in a greedy way, and it's flat/plateau-y. Gini/entropy reduction is smooth-ish and correlates with how "clean" the classes become.

## Strengths & weaknesses

- **+** Interpretable (you can read the rules), no feature scaling needed, handles non-linearities and interactions naturally.
- **+** Base learner for ensembles — see [Gradient-boosting](Gradient-boosting.md), [XGBoost](XGBoost.md), [LightGBM](LightGBM.md), random forests, and the [base-esitmator](base-esitmator.md) role.
- **−** Deep single trees overfit badly; unstable (small data changes flip splits).
- **−** Greedy, axis-aligned splits (a linear boundary needs many splits).

## In sklearn

`DecisionTreeClassifier` / `DecisionTreeRegressor`, tuning via `max_depth`, `min_samples_split`, `ccp_alpha` (cost-complexity pruning).
