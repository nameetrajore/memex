---
title: "Vector Embeddings"
type: concept
tags: [search, machine-learning, embeddings, semantic-search, information-retrieval]
created: 2026-05-13
updated: 2026-05-13
sources: [qmd]
---

## Summary

Vector embeddings are fixed-dimensional numerical representations of text (or other data) that capture semantic meaning. Similar concepts map to nearby points in vector space, enabling semantic search via cosine similarity or other distance metrics. They are the foundation of modern information retrieval beyond keyword matching.

## How They Work

An embedding model (neural network) maps text to a dense vector — typically 256–3072 dimensions. The model is trained so that semantically similar texts produce vectors with high cosine similarity.

```
"caching strategies"     → [0.12, -0.45, 0.78, ...]
"cache eviction policy"  → [0.11, -0.43, 0.80, ...]   ← close (similar meaning)
"database sharding"      → [-0.33, 0.67, -0.12, ...]  ← far (different topic)
```

## Chunking

Documents are typically too long to embed as a single vector. They're split into chunks (~500–1000 tokens) and each chunk gets its own vector. [[QMD]] uses ~900-token chunks with 15% overlap, splitting at natural markdown boundaries (headings, code blocks, paragraphs) via a scoring algorithm. For code files, tree-sitter AST parsing provides function/class boundary-aware chunking.

## Retrieval

At query time, the query is embedded with the same model, then compared against all stored chunk vectors. Results are ranked by cosine similarity: `similarity = 1 / (1 + cosine_distance)`.

Vector search excels at finding semantically related content even when exact keywords don't match — e.g., searching "how users log in" finds documents about "authentication flow".

## Storage

Vector indexes require specialized data structures for efficient nearest-neighbor search:

| Approach | Used By | Notes |
|----------|---------|-------|
| **sqlite-vec** | [[QMD]] | SQLite extension. Simple, local, good for <1M vectors. |
| **HNSW** | Pinecone, pgvector | Graph-based approximate NN. Fast at scale. |
| **IVF** | FAISS | Inverted file index. Clusters + scan. |
| **Flat (brute-force)** | Small datasets | Exact NN. O(n) per query. |

## Models

| Model | Dimensions | Strengths |
|-------|-----------|-----------|
| embeddinggemma-300M | ~256 | Small, English-optimized. Used by [[QMD]]. |
| Qwen3-Embedding-0.6B | ~384 | Multilingual (119 languages). |
| text-embedding-3-large | 3072 | OpenAI. High quality. Cloud-only. |

## Limitations

- **No exact match**: Embeddings can miss precise keywords, acronyms, or IDs. This is why [[Hybrid Search]] combines vectors with BM25.
- **Model-dependent**: Vectors from different models are incompatible. Changing models requires re-embedding everything.
- **Chunk boundaries**: Poor chunking degrades retrieval quality. Smart boundary detection matters.

## Related

- [[QMD]] — uses embeddinggemma-300M for local vector search
- [[Hybrid Search]] — combines vector search with BM25 for comprehensive retrieval
- [[Database Indexing]] — vector indexes are a specialized index type alongside B-tree and inverted indexes
