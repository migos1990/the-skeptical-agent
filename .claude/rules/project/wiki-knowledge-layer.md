# Wiki Knowledge Layer — LLM-Maintained Knowledge System

A persistent, compounding knowledge layer maintained by LLMs. Inspired by Andrej Karpathy's LLM Wiki pattern: instead of chunking raw documents and retrieving at query time, an LLM incrementally builds and maintains a structured wiki with summaries, entity pages, and cross-references.

Reference: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## Architecture

Three layers:

```
Layer 1: RAW INPUTS (immutable — humans write, LLM reads)
  input/               <- Drop any file: notes, docs, screenshots, transcripts
                          LLM reads but never modifies these

Layer 2: THE WIKI (LLM-maintained — structured knowledge)
  wiki/                <- LLM-generated markdown with frontmatter
    _master-index.md      Topic TOC, open questions, hot signals
    <topic>/
      _index.md           Topic summary + page list
      <page>.md           Individual fact pages with metadata

Layer 3: QUERY OUTPUTS (skill results that cite the wiki)
  output/              <- Dated skill outputs
```

## Page Schema

Every wiki page requires structured frontmatter:

```markdown
---
topic: architecture
title: Current State
confidence: HIGH | MEDIUM | LOW
corroboration: established | corroborated | uncorroborated | inferred
last_verified: 2026-04-03
sources:
  - file: 2026-03-15-discovery.md
    type: human-verbal | human-written | web-research | scan-data | doc-verified
    who: "Jane Doe, IT Director"
    independent: true
  - file: 2026-04-01-review.md
    type: web-research
    who: null
    independent: true
stale_after: 90d
tags: [relevant, searchable, tags]
links:
  - architecture/integration-patterns.md
backlinks:
  - competitive/competitor-posture.md
---
```

## Corroboration Levels

| Independent Sources | Level | Tag | Behavior |
|---|---|---|---|
| 0 | inferred | `[INFERRED — 0 sources]` | Hypothesis only. Never base recommendations on this alone. |
| 1 | uncorroborated | `[UNCORROBORATED — single source]` | Usable but MUST be flagged. Probe for confirmation when consequential. |
| 2 | corroborated | `[CORROBORATED — 2 sources]` | Reliable for recommendations. Still challengeable. |
| 3+ | established | No tag needed | Established fact. Overridable only by contradicting Tier 1 evidence. |

## Independent Source Truth Table

Not all sources are independent. Two readings of the same original data do not constitute corroboration.

| Source A | Source B | Independent? |
|---|---|---|
| Same person, different calls | Same person | No — same observer |
| Different person confirming same fact | Original claimant | Yes — different observer |
| Human claim | Web research confirming | Yes — different channel |
| Human claim | Machine scan confirming | Yes — strongest corroboration |
| Same document processed twice | Same document | No — reprocessed, not independent |

**The test:** Could Source B have produced its data without any knowledge of Source A? If yes, independent. If no, not.

## Reader-Side Skepticism

When an agent reads a wiki page, it applies these checks in order:

### Check 1: Corroboration Tag Check
For `uncorroborated` or `inferred` facts, the reader MUST:
1. Attempt independent verification (web search, doc check), OR
2. Flag visibly in output so the user sees it, OR
3. Probe the user: "The wiki says X based on a single source. Can you confirm?"

### Check 2: Staleness + Corroboration Compound
Staleness and low corroboration compound risk:
- Stale + uncorroborated = highest risk. Do not use in recommendations without fresh verification.
- Fresh + established = use freely.

### Check 3: Contradiction Detection
When research contradicts a wiki fact:
1. Do NOT silently override the wiki
2. Do NOT silently accept the wiki over fresher evidence
3. Surface both with provenance: "Wiki says X (from Jane, March). My research found Y (from source, today). Which is correct?"

### Check 4: "What If Wrong?" Impact Test
For the 2-3 most consequential facts in any output: "If this fact is wrong, does my recommendation change?" If YES, extra scrutiny regardless of corroboration level.

## Write Path

### Who Writes the Wiki
Only a dedicated sync process writes to the wiki. Other skills produce outputs. The sync reads those outputs and consolidates facts into wiki pages.

### Fact Extraction Rules
From saved outputs, extract:
- New quantitative claims (numbers + units) not already in wiki
- New named entities (people, tools, competitors)
- New relationships (X uses Y, A reports to B)
- Corrections to existing wiki facts

Each fact carries source metadata: type, who, independent flag.

Ignore:
- Recommendations, hypotheticals, "could"/"should" statements
- Marketing language
- Facts about your own products (those belong in reference docs, not the wiki)

### Staleness Rules
A page is stale when: `today - last_verified > stale_after`.

Stale pages are:
- Flagged in their index with `[STALE]`
- Surfaced in the master index
- Never auto-deleted — only refreshed via sync

## The Poisoning Problem

This is why the corroboration system exists. Two corruption vectors:

1. **Human corruption** — Someone says something on a call. It gets transcribed and ingested as fact. Except it was wrong, aspirational, or a guess. The wiki doesn't know the difference.

2. **Skill corruption** — An automated research skill pulls data. Some is outdated or misattributed. The wiki absorbs it. Every downstream reader inherits the error.

**Guardrails:**
- Source trust hierarchy (see `source-trust-hierarchy.md`) classifies every input
- Corroboration tracking prevents single-source claims from being treated as established
- Document-claim framing treats ingested files as claims, not facts
- Adversarial review (see `adversarial-review.md`) catches propagated errors in outputs

## Relationship to Other Rules

| Rule | Relationship |
|------|-------------|
| `source-trust-hierarchy.md` | Classifies sources. Wiki tracks corroboration (independent confirmations). Both apply. |
| `adversarial-review.md` | Wiki sync output goes through adversarial review. Individual pages do not. |
| `learnings.md` | Wiki = human-readable fact synthesis. Learnings = structured event log. Wiki answers "what"; learnings answers "what changed." |
