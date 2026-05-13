---
title: "QMD"
type: source
tags: [search, hybrid-search, vector-embeddings, bm25, local-ai, mcp, knowledge-management]
created: 2026-05-13
updated: 2026-05-13
sources: [qmd]
---

## Summary

QMD (Query Markup Documents) is an on-device hybrid search engine for markdown notes, documentation, and knowledge bases. It combines BM25 full-text search, [[Vector Embeddings|vector semantic search]], and LLM re-ranking into a single [[Hybrid Search]] pipeline — all running locally via node-llama-cpp with GGUF models. QMD is the search backbone of this Memex vault.

## Architecture

QMD stores its index in SQLite (`~/.cache/qmd/index.sqlite`) using three key extensions:

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Full-text index | SQLite FTS5 (BM25) | Fast keyword search — same inverted index approach as [[Database Indexing\|Elasticsearch]] |
| Vector index | sqlite-vec | Cosine similarity over [[Vector Embeddings]] |
| LLM cache | SQLite table | Cached query expansion and re-rank scores |

Three local GGUF models power the pipeline:

| Model | Purpose | Size |
|-------|---------|------|
| embeddinggemma-300M | [[Vector Embeddings]] generation | ~300 MB |
| qwen3-reranker-0.6B | LLM re-ranking (yes/no + logprobs) | ~640 MB |
| qmd-query-expansion-1.7B | Query expansion (fine-tuned) | ~1.1 GB |

## Search Modes

```
search   → BM25 full-text only (fast, keyword-based)
vsearch  → Vector semantic only (embedding similarity)
query    → Hybrid: FTS + Vector + Query Expansion + Re-ranking (best quality)
```

The `query` pipeline is the most interesting — it implements [[Hybrid Search]] with [[Reciprocal Rank Fusion]]:

1. **Query Expansion**: The original query (weighted ×2) plus 1 LLM-generated variation.
2. **Parallel Retrieval**: Each query searches both BM25 and vector indexes.
3. **RRF Fusion**: `score = Σ(1/(k+rank+1))` where k=60. Top-rank bonus: +0.05 for #1, +0.02 for #2–3.
4. **Top-K Selection**: Top 30 candidates forwarded to re-ranking.
5. **LLM Re-ranking**: qwen3-reranker scores each document (yes/no with logprob confidence).
6. **Position-Aware Blending**: RRF rank 1–3 → 75% retrieval / 25% reranker; rank 4–10 → 60/40; rank 11+ → 40/60.

> [!note] Why Position-Aware Blending?
> Pure RRF can dilute exact keyword matches when expanded queries don't match. The top-rank bonus preserves documents that score #1 for the original query. Position-aware blending prevents the reranker from overriding high-confidence retrieval results.

## Smart Chunking

Documents are split into ~900-token chunks with 15% overlap. QMD uses a scoring algorithm to find natural markdown break points (headings score 50–100, code block boundaries 80, blank lines 20). A squared distance decay ensures a heading 200 tokens back still beats a line break at the target.

For code files (`.ts`, `.js`, `.py`, `.go`, `.rs`), AST-aware chunking via tree-sitter splits at function/class/import boundaries — producing higher-quality chunks than arbitrary text cuts.

## Collection & Context System

QMD organizes content into **collections** (directories with glob patterns) and **contexts** (descriptive metadata attached to paths). Context is returned alongside search results, giving LLMs richer information for making retrieval decisions.

```sh
qmd collection add ~/notes --name notes
qmd context add qmd://notes "Personal notes and ideas"
```

## Integration: CLI, MCP, and SDK

- **CLI**: `qmd search`, `qmd vsearch`, `qmd query`, `qmd get`, `qmd multi-get`
- **MCP Server**: Exposes `query`, `get`, `multi_get`, `status` tools via Model Context Protocol (stdio or HTTP transport). Compatible with Claude Desktop and Claude Code.
- **SDK**: `npm install @tobilu/qmd` — use `createStore()` for programmatic access with full TypeScript types.

The MCP HTTP transport keeps LLM models loaded in VRAM across requests, avoiding repeated model loading (~1s cold start penalty only after 5 min idle).

## Score Interpretation

| Score | Meaning |
|-------|---------|
| 0.8–1.0 | Highly relevant |
| 0.5–0.8 | Moderately relevant |
| 0.2–0.5 | Somewhat relevant |
| 0.0–0.2 | Low relevance |

## Connection to This Vault

This Memex vault uses QMD as its search layer. The CLAUDE.md schema directs the LLM to use `qmd query`, `qmd search`, and `qmd vsearch` for the QUERY operation, and to run `qmd update && qmd embed` after every ingest to keep the index current. QMD enables the [[LLM Wiki]] pattern to scale beyond what a flat `index.md` catalog can support.

## Related

- [[LLM Wiki]] — the pattern this vault instantiates; QMD is its search layer
- [[Hybrid Search]] — the search paradigm QMD implements
- [[Vector Embeddings]] — the embedding technique powering semantic search
- [[Database Indexing]] — QMD uses FTS5 (inverted index) and sqlite-vec (vector index)
- [[Caching]] — QMD caches LLM responses (query expansion, re-rank scores) in SQLite
- [[Reciprocal Rank Fusion]] — the fusion strategy for combining ranked lists
