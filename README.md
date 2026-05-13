# Memex

My personal knowledge base — a persistent, LLM-maintained wiki that grows with everything I read.

Inspired by Vannevar Bush's [Memex (1945)](https://en.wikipedia.org/wiki/Memex): a personal knowledge store where the connections between documents are as valuable as the documents themselves. Bush couldn't solve the maintenance problem. The LLM handles that.

## How it works

- `raw/` — articles, papers, transcripts, notes. I add things here; the LLM reads them.
- `wiki/` — structured, interlinked markdown pages the LLM writes and maintains. Concept pages, source summaries, entity pages, analyses.

When I drop something into `raw/`, the LLM reads it, extracts what matters, and weaves it into the wiki — updating existing pages, creating new ones, cross-referencing, flagging contradictions. The wiki compounds over time. I never write it myself.

I browse the wiki in [Obsidian](https://obsidian.md). The LLM is the programmer; the wiki is the codebase.

## Structure

```
Memex/
├── CLAUDE.md          ← schema: workflows, conventions, rules for the LLM
├── raw/               ← source documents (immutable)
└── wiki/
    ├── index.md       ← catalog of all pages
    ├── log.md         ← chronological record of every operation
    ├── overview.md    ← evolving synthesis
    ├── sources/       ← one summary page per raw source
    ├── concepts/      ← ideas, frameworks, terms
    ├── entities/      ← people, orgs, works
    └── analyses/      ← comparisons, answers, syntheses
```

## Search

Uses [qmd](https://github.com/toblu/qmd) for hybrid BM25 + vector search over the wiki. Models run fully on-device.

```bash
qmd query "caching strategies eviction policies"
qmd search "LRU"
qmd vsearch "when should I use a CDN"
```
