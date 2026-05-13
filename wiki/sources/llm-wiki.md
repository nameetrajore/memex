---
title: "LLM Wiki"
type: source
tags: [knowledge-management, llm, obsidian, personal-knowledge-base, meta]
created: 2026-05-13
updated: 2026-05-13
sources: [llm-wiki]
---

## Summary

A pattern document by [[Andrej Karpathy]] describing how to use LLMs to build and maintain persistent, compounding personal knowledge bases. Instead of RAG (re-deriving knowledge each query), the LLM incrementally builds a structured wiki from raw sources — summarizing, cross-referencing, and filing knowledge once, then keeping it current. This is the foundational pattern behind this Memex vault.

## The Core Idea

Most LLM-document workflows are RAG: upload files, retrieve chunks at query time, generate an answer. The LLM rediscovers knowledge from scratch every time. Nothing compounds.

The LLM Wiki inverts this: when a new source arrives, the LLM **reads it, extracts key information, and integrates it into an existing wiki** — updating entity pages, revising summaries, noting contradictions, strengthening synthesis. The wiki is a **persistent, compounding artifact**. Cross-references are already there. Contradictions are already flagged. The synthesis already reflects everything ingested.

**You never write the wiki yourself.** The human curates sources, asks questions, and directs analysis. The LLM does the summarizing, cross-referencing, filing, and bookkeeping.

## Architecture

Three layers:

1. **Raw sources** — immutable source documents (articles, papers, images). The LLM reads but never modifies. Source of truth.
2. **The wiki** — LLM-generated markdown files (summaries, entity pages, concept pages, comparisons, overview, synthesis). The LLM owns this entirely.
3. **The schema** — a configuration document (CLAUDE.md, AGENTS.md) that tells the LLM how the wiki is structured, what conventions to follow, and what workflows to execute. Human and LLM co-evolve this over time.

## Operations

| Operation | What Happens |
|-----------|-------------|
| **Ingest** | LLM reads a new source, writes a summary page, updates index, updates entity/concept pages across the wiki, appends to log. A single source may touch 10–15 pages. |
| **Query** | LLM searches index for relevant pages, reads them, synthesizes an answer with citations. Good answers get filed back as new wiki pages — explorations compound. |
| **Lint** | Health-check: find contradictions, stale claims, orphan pages, missing concepts, missing cross-references, data gaps. Suggest new sources to investigate. |

## Indexing and Logging

- **index.md** — content-oriented catalog of all wiki pages. LLM reads it first to find relevant pages for queries. Works well at moderate scale (~100 sources, hundreds of pages) without embedding infrastructure.
- **log.md** — chronological, append-only record of operations. Parseable with simple tools if entries use consistent prefixes.

## Why This Works

The tedious part of knowledge bases is bookkeeping — updating cross-references, keeping summaries current, noting contradictions. Humans abandon wikis because maintenance burden grows faster than value. **LLMs don't get bored** and can touch 15 files in one pass. The wiki stays maintained because maintenance cost is near zero.

The human's job: curate sources, direct analysis, ask good questions, think about meaning.
The LLM's job: everything else.

## Connection to [[Vannevar Bush]]'s Memex

The idea descends from Bush's 1945 vision of the Memex — a personal, curated knowledge store with associative trails between documents. Bush's vision was closer to this than to the web: private, actively curated, with connections as valuable as the documents themselves. The part Bush couldn't solve was who does the maintenance. The LLM handles that.

## Tooling Notes

- **Obsidian** as the IDE for browsing the wiki (graph view, wikilinks, plugins).
- **Obsidian Web Clipper** for converting web articles to markdown sources.
- **Marp** for generating slide decks from wiki content.
- **Dataview** for querying page frontmatter.
- **qmd** — local markdown search engine with hybrid BM25/vector search (CLI + MCP server).
- The wiki is just a git repo of markdown files — version history and collaboration for free.

## Related

- [[Andrej Karpathy]]
- [[Vannevar Bush]]
- [[Hello Interview]]
