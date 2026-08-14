# Model interpretability

Interpretability answers **"why did the model predict this?"** — essential for debugging, trust, regulation (GDPR explanations), and finding leakage. There's no single answer; the right tool depends on the model and the question.

## Model-specific (inherent) methods

- **Linear models** — coefficients are directly readable: `e^w` for logistic = multiplicative change in odds (see [logistic-regression](logistic-regression.md)). Standardize features first, or coefficients aren't comparable.
- **Trees / boosting** — the decision path is literally the reasoning. **Feature importance** (total impurity reduction / split count) ranks features cheaply, though it's biased toward high-cardinality features.
- **Neural nets** — hardest. Saliency maps (gradients), attention weights, or neuron analysis — all approximate.

## Model-agnostic methods (work on any model)

- **Permutation importance** — shuffle one feature, measure the drop in score. If shuffling barely hurts, the model isn't using it. Honest (uses the real metric), but correlated features make it misleading (dropping either one looks important).
- **Partial dependence plots (PDP)** — vary one feature, average the prediction over the rest. Shows the *direction* of an effect (unlike permutation importance).
- **SHAP values** — the modern standard. Each feature gets a contribution score for a *single prediction* that sums to the prediction minus the baseline. Based on game theory (Shapley values), consistent and theoretically grounded — but O(2^d) in the worst case, so implemented with approximations.

## The SHAP mental model

For prediction `f(x)`, find `φ₀ + Σ φᵢ = f(x)` where `φᵢ` is feature `i`'s contribution. Compare against a baseline (average prediction). Feature `i`'s contribution = the marginal effect of adding it, averaged over all orderings of the other features. That average-over-orderings is why it's expensive and why it's "fair" — see [bias-variance-tradeoff](bias-variance-tradeoff.md) for the related averaging intuition.

## When to use what

- Global (which features matter overall): permutation importance / SHAP summary plot / feature importance.
- Local (why *this* prediction): SHAP force plot, LIME, or a decision path.
- Direction/effect: PDP (careful: assumes no feature interactions).

## Related

- [tabular-regression](tabular-regression.md) — interpretability matters most on structured data.
- [ranking](ranking.md) — feature importance feeds into feature engineering for search.
- [decision-trees](decision-trees.md) — the most interpretable base models.
