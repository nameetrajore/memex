---
title: "Hybrid Search"
type: concept
tags: [search, information-retrieval, bm25, vector-search, re-ranking]
created: 2026-05-13
updated: 2026-05-13
sources: [qmd]
---

## Summary

Hybrid search combines keyword-based retrieval (BM25) with vector semantic search and optional LLM re-ranking to get the best of both worlds: precise keyword matching and semantic understanding. Results from multiple retrieval backends are fused — typically via [[Reciprocal Rank Fusion]] — then optionally re-ranked by a cross-encoder or LLM for final ordering.

## Why Hybrid?

Neither keyword search nor vector search alone is sufficient:

| Approach | Strengths | Weaknesses |
|----------|-----------|------------|
| **BM25 (keyword)** | Exact match, acronyms, IDs, code symbols. Fast. No model needed. | Misses synonyms and paraphrases. |
| **Vector (semantic)** | Understands meaning, paraphrases, conceptual similarity. | Can miss exact keywords. Slower. Needs embedding model. |
| **Hybrid** | Gets both exact matches and semantic matches. | More complex pipeline. Needs a fusion strategy. |

A query like `"LRU cache eviction"` benefits from BM25 finding documents with the exact term "LRU", while vector search finds documents that discuss "least recently used removal policy" without using that acronym.

## The Pipeline

A typical hybrid search pipeline (as implemented by [[QMD]]):

1. **Query Expansion** — An LLM generates 1–2 reformulations of the user query to broaden recall.
2. **Parallel Retrieval** — Each query variant runs against both BM25 and vector indexes.
3. **Fusion** — [[Reciprocal Rank Fusion]] merges all ranked lists into a single ordering.
4. **Re-ranking** — A cross-encoder or LLM scores the top-K candidates for final relevance.
5. **Blending** — Position-aware blending balances retrieval confidence vs. reranker confidence.

## Reciprocal Rank Fusion (RRF)

The standard fusion method. For each document, sum `1/(k + rank + 1)` across all ranked lists where it appears (k is typically 60). Documents that rank highly across multiple lists surface to the top. RRF is model-free, parameter-light, and works well even when score scales differ between backends.

## Re-ranking

After fusion produces ~30 candidates, a cross-encoder (or small LLM) evaluates each document against the query. Unlike bi-encoder embeddings (which encode query and document independently), cross-encoders attend to both simultaneously — yielding higher accuracy at the cost of latency. QMD uses qwen3-reranker-0.6B for this step.

## When to Use Hybrid Search

- **Knowledge bases and documentation** — users search with both exact terms and natural questions.
- **Code search** — function names need keyword match; "how to handle errors" needs semantics.
- **Enterprise search** — diverse content types where no single retrieval method dominates.

## Related

- [[QMD]] — on-device hybrid search engine implementing this pattern
- [[Reciprocal Rank Fusion]] — the fusion strategy for merging ranked lists
- [[Vector Embeddings]] — the representation powering semantic search
- [[Database Indexing]] — BM25 relies on inverted indexes (FTS5)
- [[Caching]] — search systems cache embeddings and LLM scores for performance
