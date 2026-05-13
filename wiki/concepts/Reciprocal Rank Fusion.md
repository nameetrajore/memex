---
title: "Reciprocal Rank Fusion"
type: concept
tags: [search, information-retrieval, fusion, ranking]
created: 2026-05-13
updated: 2026-05-13
sources: [qmd]
---

## Summary

Reciprocal Rank Fusion (RRF) is a method for combining multiple ranked result lists into a single ordering. It is score-agnostic — it uses only rank positions, not raw scores — making it ideal for merging results from backends with incompatible score scales (e.g., BM25 vs. cosine similarity).

## Formula

For each document `d`, the RRF score is:

```
RRF(d) = Σ  1 / (k + rank_i(d) + 1)
```

Where `k` is a constant (typically 60) and `rank_i(d)` is the document's rank in list `i`. Documents that appear in multiple lists accumulate score from each. The constant `k` dampens the advantage of very high ranks, preventing a single #1 result from dominating.

## Why It Works

- **Score-agnostic**: BM25 scores might range 0–25, while cosine similarity ranges 0–1. RRF doesn't care — it only looks at position.
- **Robust**: A document ranked top-5 in multiple independent lists is likely relevant, even if any single ranking is noisy.
- **Simple**: No learned parameters, no training data needed.

## Enhancements in QMD

[[QMD]] extends standard RRF with:

- **Query weighting**: The original query contributes ×2 weight (counted twice in fusion).
- **Top-rank bonus**: Documents ranked #1 in any list get +0.05; #2–3 get +0.02.
- **Position-aware blending**: After LLM re-ranking, the final score blends RRF and reranker scores based on RRF position (75/25 for top 3, 60/40 for 4–10, 40/60 for 11+).

## Related

- [[Hybrid Search]] — the search paradigm that uses RRF for fusion
- [[QMD]] — implements RRF with position-aware enhancements
- [[Database Indexing]] — the underlying indexes whose results RRF merges
