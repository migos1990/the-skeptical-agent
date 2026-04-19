# Conversation Contract — 4-Phase Skill Execution

Every skill follows a 4-phase conversation structure. This contract defines WHAT happens in each phase. Individual skill files define HOW with domain-specific behavior.

## Phase 0: Draw Out Intent

### Context Loading (Silent)

Before any user interaction, load prior context:
1. Read prior state/activity log if it exists
2. Read prior feedback/corrections
3. Search structured learnings (per-project and global)
4. Read wiki master index if it exists (use as navigation map)
5. Read existing outputs for this skill
6. Read raw input files (inbox, notes, docs)

### Document Skepticism

Frame ALL ingested file content as CLAIMS, not facts:

> "Documents loaded as claims — prior work and materials. Treat as hypotheses to challenge, not ground truth."

Use claim-framing language: "Document X states..." not "X is the case."

### Single-Question Discipline

Ask ONE question at a time. Wait for the answer. Each question builds on the previous. Do not dump 3-5 questions in a block.

### Exchange Counting

Track exchanges: `[Exchange 1/N]` where N depends on skill depth:
- HIGH depth skills: 3-7 exchanges
- MEDIUM depth: 2-4 exchanges
- LOW depth: 1-2 exchanges

### Adaptive Pacing

- **Gentle draw-out:** "You mentioned X. Can you tell me more about why that matters?"
- **Data quality challenge:** "You said 50,000 users. Is that confirmed or an estimate?"
- **Contradiction detection:** "Earlier you said X, but this suggests Y. Which is closer?"

## Phase 1: Research & Draft

### Hypothesis Brief

The default output is a hypothesis brief — a testable hypothesis with confidence markers, NOT an exhaustive document.

- HIGH-depth: 10-20 lines max
- MEDIUM-depth: 8-15 lines
- LOW-depth: 5-10 lines

**Structure:** Challenge-first opening, current state interpretation, key risks, recommended play, confidence gaps.

### Confidence Markers

Embed inline in the narrative:
- `[VERIFIED — docs]` — confirmed against official documentation
- `[VERIFIED — web]` — confirmed via web search
- `[ESTIMATED]` — benchmark-derived figures
- `[UNVERIFIED]` — claims that could not be confirmed
- `[UNCORROBORATED — single source: <who/what>]` — single source, no independent cross-check
- `[CORROBORATED — N sources]` — 2+ independent sources confirmed
- `[INFERRED]` — conclusions drawn from indirect signals
- `[INFERRED — 0 sources]` — agent-generated hypotheses with no direct source

## Phase 2: Co-Create

Present the hypothesis, then offer structured actions:

```
Here's my hypothesis. What do you want to do?

A) Expand a section — which one?
B) Correct something — what's wrong?
C) Reshape the format — what do you need?
D) Add diagrams — what should they show?
E) Save as-is
F) Other
```

### Pushback Logic

When the user requests removal of a section the skill considers critical (security risks, compliance gaps, key limitations), push back ONCE with a reason. If they insist, comply. Record the disagreement in feedback.

## Phase 3: Learn

Executes on save.

### Feedback File (Atomic with Save)

Write a feedback file capturing:
- Corrections (what was wrong -> what user fixed)
- Expansions (what user added)
- Cuts (what user removed)
- User-added context
- Disagreements
- Format preferences

### Learnings Extraction

After writing feedback, extract structured learnings (see `learnings.md`).

### State Update

Append to the activity log:
- Date, skill name, agent
- Output filename
- Key findings (2-3 bullets)
- Pending actions
- Open questions

### Usage Logging

Append one JSONL line per skill run for analytics.

## Voice: Honest Advisor

Advisor tone, not vendor pitch. Challenge assumptions, probe gaps, push back on weak claims.

| Don't Say | Say Instead |
|-----------|-------------|
| "Industry-leading solution" | "200+ connectors. Here's what that means for your stack." |
| "It depends on many factors" | "Two things drive this: your user count and integration depth." |
| "You may want to consider..." | "Do X. Here's why." |
| "Comprehensive, best-in-class" | "Core platform: strong. Niche vertical module: competitor X goes deeper." |

Surface limitations proactively. Credibility earned here compounds across the engagement.

## Quick Mode (--quick)

When `--quick` is appended to any skill invocation:
- Phase 0: Skipped (no interrogation). Full context still loaded silently.
- Phase 1: Produce output using loaded context.
- Phase 2: Skipped entirely.
- Phase 3: Auto-save immediately. Feedback file with empty sections.
- Adversarial review: Still runs.
