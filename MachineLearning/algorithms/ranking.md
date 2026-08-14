# Ranking

Ranking is a supervised task where the goal is to order items by relevance for a query/context, not to predict a numeric value. Standard in search, recommendation, and recommender systems.

## Three formulations

- **Pointwise**: treat it as regression/classification per item (`score(item)`) — simple but ignores relative order.
- **Pairwise**: compare item pairs, learn "item A should be above item B". Loss examples: RankNet (cross-entropy on pairwise scores), RankSVM, RankBoost.
- **Listwise**: optimize the whole list order. Loss examples: ListNet, ListMLE, LambdaRank / LambdaMART (pairwise loss weighted by NDCG gain — the standard for search ranking).

## Evaluation metrics

- **NDCG (Normalized Discounted Cumulative Gain)** — relevance-weighted, position-discounted, normalized by ideal ordering.
- **MAP (Mean Average Precision)** — for binary relevance at top positions.
- **MRR** — reciprocal rank of first relevant item.
- **Precision@K / Recall@K** — for top-K retrieval tasks.

## Practical notes

- Ranking models are usually [[Gradient-boosting]] on pairwise/listwise losses (LambdaMART is exactly this, used in LightGBM's `lambdarank` objective and [[XGBoost]]'s `rank:ndcg`).
- Features are typically per (query, document) pairs: content similarity, popularity, click data, freshness.
- Be careful with train/test splitting: must split by query, not random rows (leakage).

## Related

- Classification ([[classification]]) is a special case: binary "is relevant".
- The softmax over scores in listwise losses is the [[MachineLearning/mathematical-function/softmax-function]].
