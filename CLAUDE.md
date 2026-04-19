# The Skeptical Agent — Drop-In Quality Framework

A curated set of `.md` rule files for Claude Code (or any AI coding agent) that enforce epistemic rigor, source verification, adversarial review, and knowledge system integrity.

## What This Is

Rules that make AI agents more honest and more accurate. Drop them into your project's `.claude/rules/` directory and they auto-load every session.

Extracted from a production multi-agent system with 13 specialized agents, 74 skill files, and a persistent knowledge layer. The rules evolved from watching agents silently produce confident-sounding garbage — and building guardrails to stop it.

## Quick Start

```bash
# Clone into your project
git clone https://github.com/<your-username>/claude-agent-rules.git /tmp/rules

# Copy the rules you want
cp -r /tmp/rules/.claude/rules/common/ your-project/.claude/rules/common/
cp -r /tmp/rules/.claude/rules/project/ your-project/.claude/rules/project/

# Or cherry-pick individual files
cp /tmp/rules/.claude/rules/project/source-trust-hierarchy.md your-project/.claude/rules/
```

Rules in `.claude/rules/` are auto-loaded every Claude Code session. No configuration needed.

## Structure

```
.claude/rules/
  common/              <- Universal development rules (any project)
    coding-style.md       Immutability, file organization, error handling
    testing.md            80%+ coverage, TDD workflow
    security.md           Pre-commit security checklist
    git-workflow.md       Conventional commits, PR process
    development-workflow.md  Research > Plan > TDD > Review > Commit
    agents.md             Agent orchestration and parallel execution
    patterns.md           Skeleton projects, repository pattern, API envelopes
    performance.md        Model selection, context window management
    hooks.md              Hook types and permission best practices

  project/             <- Knowledge system & quality rules (agent-heavy projects)
    source-trust-hierarchy.md   4-tier source classification with verification rules
    adversarial-review.md       Multi-model review gate (Santa Method)
    wiki-knowledge-layer.md     LLM-maintained wiki with corroboration tracking
    conversation-contract.md    4-phase skill execution with epistemic markers
    learnings.md                Cross-session structured learning extraction

examples/
  skill/
    knowledge-audit.md  Audit skill — finds poisoned data and false corroboration
```

## Core Philosophy

1. **Facts-first** — Every claim must have a source. Mark gaps: `[UNVERIFIED]`, `[ESTIMATED]`, `[INFERRED]`.
2. **Epistemic skepticism** — Trust nothing by default. Cross-reference. Flag discrepancies.
3. **Not all sources are equal** — Official docs > human claims > web research > training data.
4. **Corroboration over confidence** — One source is a claim. Two independent sources is corroborated. Three is established.
5. **Adversarial review** — A single reviewer shares the generator's blind spots. Multiple independent reviewers break this.

## The Rules Explained

### Source Trust Hierarchy

Classifies every information source into 4 tiers:
- **Tier 1: TRUST** — Official docs, SEC filings, published analyst reports
- **Tier 2: VERIFY** — Marketing materials, internal docs, blog posts
- **Tier 3: SKEPTICISM** — Training data, web search, competitor marketing, forums
- **Tier 4: HANDLE WITH CARE** — Roadmap, pre-release, pricing, NDA content

The iron rule: *If it's not in an official doc, don't claim it as fact.*

### Adversarial Review (Santa Method)

Every output goes to 2+ independent challenger models. Both must pass all criteria. If any fails:
1. Collect all failures
2. Apply targeted fixes (only what failed — no scope creep)
3. Re-run ALL reviewers on the fixed version (fresh context, no memory of Round 1)
4. Max 2 rounds, then escalate to human

Criteria: factual accuracy, source citations, logical consistency, vendor objectivity, epistemic honesty, sharpness (no marketing language), corroboration integrity.

### Wiki Knowledge Layer

Inspired by Andrej Karpathy's LLM Wiki pattern. Three layers:
1. **Raw inputs** — immutable source files
2. **LLM-maintained wiki** — structured markdown with frontmatter (confidence, corroboration, sources, staleness)
3. **Query outputs** — skill results that cite the wiki

Every wiki page tracks: confidence level, corroboration status, source provenance, staleness threshold. The system distinguishes between `established` (3+ independent sources), `corroborated` (2), `uncorroborated` (1), and `inferred` (0) facts.

### Conversation Contract

4-phase execution for any skill:
- **Phase 0: Draw Out Intent** — Load context, ask questions, classify intent
- **Phase 1: Research & Draft** — Produce hypothesis with confidence markers
- **Phase 2: Co-Create** — Interactive refinement with pushback on bad ideas
- **Phase 3: Learn** — Extract learnings, update state, log usage

### Learnings System

Structured observations extracted from user corrections and review calibrations:
- Per-project learnings (corrections, preferences)
- Global learnings (patterns, calibrations, competitive intel)
- Confidence scoring (1-10), staleness thresholds, deduplication

## Adapting to Your Domain

These rules are domain-agnostic but were battle-tested in a presales intelligence context. To adapt:

1. **Source trust hierarchy** — Replace the example sources (vendor docs, analyst reports) with your domain's trusted sources. The 4-tier structure applies universally.
2. **Adversarial review criteria** — Add domain-specific criteria to the rubric. The Santa Method pattern works for any content type.
3. **Wiki topics** — The 6 canonical topic folders (architecture, stakeholders, competitive, compliance, value-drivers, technical-debt) are presales-flavored. Replace with your domain's knowledge categories.
4. **Skill example** — The deep-research skill shows the full pattern. Fork it for your use case.

## License

MIT. Use however you want.
