# Knowledge Base Schema

This is a persistent LLM-maintained wiki. You (Claude) own the `wiki/` layer entirely. The human curates sources and asks questions; you do the bookkeeping.

## Directory Layout

```
knowledge-base-ubuntu/
├── CLAUDE.md              ← this file: your operating instructions
├── raw/                   ← immutable source documents (never modify)
│   └── assets/            ← images downloaded from articles
└── wiki/                  ← you own everything here
    ├── index.md           ← content catalog (update on every ingest)
    ├── log.md             ← append-only chronological log
    ├── entities/          ← pages for people, organizations, places, products
    ├── concepts/          ← pages for ideas, topics, frameworks, terms
    └── sources/           ← one summary page per ingested source
```

## Page Conventions

Every wiki page starts with YAML frontmatter:

```yaml
---
title: Page Title
type: entity | concept | source | synthesis | overview
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 0          # number of sources this page draws from
---
```

- Use `[[WikiLink]]` style links between pages (Obsidian-compatible).
- Keep pages focused: one entity, one concept, one source per file.
- File names: `kebab-case.md` (e.g. `neural-scaling-laws.md`).
- Headings: H2 for major sections, H3 for subsections. No H1 (the title is in frontmatter).
- Cite sources inline: `([Source Name](../sources/source-name.md))`.
- Flag contradictions with a `> [!warning] Contradiction` callout block.
- Flag gaps / open questions with a `> [!question] Open Question` callout block.

## Workflows

### Ingest
When the human drops a new file in `raw/` and says "ingest":
1. Read the source document (and any referenced images if needed).
2. Discuss key takeaways with the human briefly before writing.
3. Write a summary page in `wiki/sources/` named after the source.
4. Create or update entity pages in `wiki/entities/` for people, orgs, places mentioned.
5. Create or update concept pages in `wiki/concepts/` for key ideas.
6. Update `wiki/index.md` — add the new source and any new pages.
7. Append an entry to `wiki/log.md` with format: `## [YYYY-MM-DD] ingest | Source Title`.
8. Tell the human which pages were created or updated.

### Query
When the human asks a question:
1. Read `wiki/index.md` to find relevant pages.
2. Read those pages and synthesize an answer with inline citations.
3. If the answer is a useful artifact (comparison, analysis, synthesis), offer to save it as a new wiki page.
4. If saved, update `wiki/index.md` and append to `wiki/log.md`.

### Lint
When the human asks for a health check:
1. Read all wiki pages via the index.
2. Report: contradictions, stale claims, orphan pages (no inbound links), concepts mentioned but lacking their own page, missing cross-references.
3. Suggest sources to seek out for identified gaps.
4. Append a lint entry to `wiki/log.md`.

## Index Format

`wiki/index.md` sections:
- **Sources** — one line per source: `- [[sources/name]] — one-line description (YYYY-MM-DD)`
- **Entities** — one line per entity page
- **Concepts** — one line per concept page
- **Syntheses** — one line per synthesis/analysis page

## Log Format

Each entry in `wiki/log.md`:
```
## [YYYY-MM-DD] <operation> | <title>

<1-3 sentence summary of what was done, what was added/changed>
```

Operations: `ingest`, `query`, `lint`, `update`.

## General Rules

- Never modify anything in `raw/`. Read-only.
- Always update `index.md` and `log.md` when you touch the wiki.
- Prefer updating existing pages over creating new ones — avoid fragmentation.
- When in doubt about structure, ask the human before writing.
- Keep pages skimmable: use bullet points and short paragraphs over dense prose.
