# The Skeptical Agent

Drop-in `.md` rule files that make AI coding agents more honest and more accurate.

Built for [Claude Code](https://claude.ai/code). Works with any AI coding agent that reads markdown instructions.

## The Problem

AI agents produce confident-sounding output. Most of it looks fine. Some of it is wrong. The agent doesn't know which is which, and neither do you — until a customer, colleague, or reviewer catches it.

These rules add guardrails:
- **Source verification** before claiming facts
- **Corroboration tracking** so you know which facts have one source vs. three
- **Adversarial review** where multiple models check each other's work
- **Document skepticism** so ingested files are treated as claims, not truth

## Quick Start

```bash
# Copy rules into your project
git clone https://github.com/migos1990/the-skeptical-agent.git /tmp/rules
cp -r /tmp/rules/.claude/ your-project/.claude/

# Or cherry-pick what you need
cp /tmp/rules/.claude/rules/project/source-trust-hierarchy.md your-project/.claude/rules/
```

Files in `.claude/rules/` auto-load every Claude Code session. No configuration needed.

## What's Inside

### Common Rules (any project)

| File | What It Does |
|------|---|
| `coding-style.md` | Immutability, small files, error handling, input validation |
| `testing.md` | 80%+ coverage, TDD workflow (RED/GREEN/IMPROVE) |
| `security.md` | Pre-commit checklist: no secrets, SQL injection, XSS, CSRF |
| `git-workflow.md` | Conventional commits, PR analysis process |
| `development-workflow.md` | Research > Plan > TDD > Review > Commit pipeline |
| `agents.md` | Agent orchestration, parallel execution patterns |
| `patterns.md` | Skeleton projects, repository pattern, API envelopes |
| `performance.md` | Model selection (Haiku/Sonnet/Opus), context management |
| `hooks.md` | Hook types, permission best practices |

### Project Rules (agent-heavy / knowledge systems)

| File | What It Does |
|------|---|
| `source-trust-hierarchy.md` | 4-tier source classification. Official docs > human claims > web > training data. |
| `adversarial-review.md` | Multi-model Santa Method. 2+ challengers, convergence loop, max 2 rounds. |
| `wiki-knowledge-layer.md` | LLM-maintained wiki with corroboration tracking per Karpathy's pattern. |
| `conversation-contract.md` | 4-phase skill execution: Intent > Research > Co-Create > Learn. |
| `learnings.md` | Cross-session structured learning extraction and deduplication. |

### Example Skill

| File | What It Does |
|------|---|
| `examples/skill/knowledge-audit.md` | Audit skill that finds poisoned data, false corroboration, and staleness cascades in your wiki. Demonstrates corroboration checks, source independence validation, and contradiction detection. |

## Origin

Extracted from a production multi-agent system with 13 specialized agents and 74 skill files. The rules evolved from watching agents silently produce confident-sounding garbage — and building guardrails to catch it.

<!-- TODO: Replace with LinkedIn post URL after publishing -->
<!-- Blog post with context: [Sunday AI Digest #1](link-to-linkedin-post) -->

Karpathy's original LLM Wiki gist that inspired the knowledge layer: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## License

MIT
