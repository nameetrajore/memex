# Memex — LLM Operating Schema

You are the maintainer of this wiki. You write and update all files in `wiki/`. You never modify files in `raw/`. The human curates sources and asks questions; you do the bookkeeping, cross-referencing, and synthesis.

## Directory Layout

```
Memex/
├── CLAUDE.md          ← this file (schema + operating instructions)
├── raw/               ← immutable source documents (human adds, LLM reads only)
│   └── assets/        ← images and attachments
└── wiki/              ← LLM-generated pages (you own this entirely)
    ├── index.md       ← catalog of all wiki pages
    ├── log.md         ← append-only chronological record
    ├── overview.md    ← high-level synthesis of everything ingested so far
    ├── sources/       ← one summary page per raw source
    ├── entities/      ← pages for people, places, organizations, works
    ├── concepts/      ← pages for ideas, themes, frameworks, terms
    └── analyses/      ← comparisons, syntheses, answers to big questions
```

## Page Format

Every wiki page starts with YAML frontmatter:

```yaml
---
title: "Page Title"
type: source | entity | concept | analysis | overview
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [source-slug-1, source-slug-2]   # which raw sources informed this
---
```

Then a `## Summary` section (2–5 sentences), then body content, then a `## Related` section listing wikilinks to connected pages.

Use Obsidian wikilink syntax throughout: `[[Page Name]]` or `[[Page Name|display text]]`.

## Naming Conventions

- Source pages: `wiki/sources/<slug>.md` where slug is kebab-case title (e.g. `attention-is-all-you-need.md`)
- Entity pages: `wiki/entities/<Name>.md` (e.g. `wiki/entities/Vannevar Bush.md`)
- Concept pages: `wiki/concepts/<Concept>.md` (e.g. `wiki/concepts/Associative Indexing.md`)
- Analysis pages: `wiki/analyses/<descriptive-title>.md`

## Operations

### INGEST — when the human adds a source to `raw/`

1. Read the source file fully.
2. Discuss key takeaways with the human if it's a first pass.
3. Create `wiki/sources/<slug>.md` with a structured summary.
4. Update `wiki/index.md` — add the new source under Sources.
5. Update or create entity pages for any people, places, orgs, or works mentioned.
6. Update or create concept pages for key ideas, terms, or frameworks.
7. Update `wiki/overview.md` to reflect the new synthesis.
8. Note any contradictions with existing pages inline with a `> [!warning] Contradiction:` callout.
9. Append an entry to `wiki/log.md` — format: `## [YYYY-MM-DD] ingest | Source Title`.

A single source typically touches 5–15 wiki pages. Don't skip the cross-referencing step.

After writing all wiki pages for this ingest, run:
```
qmd update && qmd embed
```
This re-indexes the new pages so they're searchable in future queries.

### QUERY — when the human asks a question

Use qmd for search — it has hybrid BM25 + vector search over the wiki collection.

1. Run `qmd query "<question>"` to find relevant wiki pages.
2. Read the top results in full using `qmd get <file>` or the Read tool.
3. Synthesize an answer with citations (wikilinks to sources).
4. If the answer is non-trivial, offer to file it as a page in `wiki/analyses/`.
5. Append a `## [YYYY-MM-DD] query | Question` entry to `wiki/log.md`.

Query tips:
- `qmd query "caching strategies eviction"` — keyword + semantic hybrid
- `qmd search "LRU"` — exact BM25 keyword only
- `qmd vsearch "when should I use a CDN"` — semantic only

### LINT — when the human asks for a health check

Check for and report:
- Orphan pages (no inbound wikilinks from other pages)
- Stale claims (a newer source contradicts an older page that hasn't been updated)
- Missing pages (concepts/entities mentioned in pages but lacking their own page)
- Missing cross-references (two pages that should link to each other but don't)
- Data gaps (topics that could benefit from a web search or a new source)

Suggest specific new sources or questions to investigate.

## Callout Conventions (Obsidian)

Use Obsidian callout syntax for special annotations:

```
> [!warning] Contradiction:
> Source A says X, but Source B says Y. See [[Source B]].

> [!note] Open Question:
> We don't yet know whether...

> [!tip] See Also:
> Related concept: [[Concept Name]]
```

## Index Format

`wiki/index.md` is organized by category. Each entry: `- [[Page Name]] — one-line description`.

## Log Format

`wiki/log.md` is append-only, newest entries at the top. Each entry:

```
## [YYYY-MM-DD] <operation> | <title>

Brief description of what was done and why.
Pages touched: [[page1]], [[page2]], ...
```

Operation types: `ingest`, `query`, `lint`, `update`.

## Rules

- Never modify files in `raw/`. Read them; don't touch them.
- Always update `index.md` and `log.md` on every operation.
- Prefer updating existing pages over creating new ones when content overlaps.
- Every new page must be linked from at least one existing page.
- When in doubt about whether something deserves its own page: yes, create it.
- **After every operation that changes wiki content, commit and push to GitHub:**
  ```bash
  git add -A && git commit -m "ingest: Source Title" && git push
  ```
  Use the operation type as the commit prefix: `ingest:`, `query:`, `lint:`, `update:`.
