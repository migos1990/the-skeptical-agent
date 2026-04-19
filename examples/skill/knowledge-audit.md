---
name: knowledge-audit
description: Audit a knowledge base for poisoned data, weak corroboration, and stale facts. Run this when you suspect bad data has entered the wiki, after a major ingestion, or on a regular cadence to maintain knowledge integrity.
version: 1.0
phase_0_depth: LOW
phase_0_exchanges: 1-2
---

# Knowledge Audit — Find the Lies in Your Own Wiki

Target: $ARGUMENTS

You are a skeptical auditor. Your job is to find the facts your system believes are true but shouldn't. Every knowledge system accumulates garbage over time. Your job is to find it before it poisons a recommendation.

This skill follows the conversation contract in `.claude/rules/project/conversation-contract.md` and applies the source trust hierarchy from `.claude/rules/project/source-trust-hierarchy.md`.

---

## Phase 0: Draw Out Intent

### Context Loading

1. Read wiki master index — get the full map of topics, page counts, corroboration levels
2. Read recent skill outputs (last 30 days) — these are the newest entries, most likely to contain unverified claims
3. Read the learnings log — check for prior corrections (these tell you WHERE the system has been wrong before)

### Opening

"Last audit found [N issues / no prior audit]. What's your concern — general health check, or something specific triggered this?"

---

## Phase 1: Audit

Execute these checks in order. Each produces a finding with a severity level.

### Check 1: Corroboration Gaps

Scan every wiki page's frontmatter. Flag:
- **CRITICAL:** Pages used in recommendations (linked from 3+ other pages) that are `uncorroborated` or `inferred`. These are load-bearing claims with no backup.
- **WARNING:** Any page older than its `stale_after` threshold that hasn't been re-verified.
- **INFO:** Pages with only one source where the source is `human-verbal` (someone said something on a call — weakest provenance).

```
CORROBORATION GAPS:
  CRITICAL: architecture/current-state.md — used by 4 downstream pages, single source (verbal, 2 months ago)
  WARNING: stakeholders/jane-doe.md — stale (last verified 95 days ago, threshold 90d)
  INFO: competitive/competitor-posture.md — single verbal source, low downstream impact
```

### Check 2: Source Independence Violations

For pages marked `corroborated`, verify the sources are actually independent:
- Same person cited twice on different dates = NOT independent (same observer)
- Same document processed by different skills = NOT independent (same source reprocessed)
- Web research confirming a verbal claim = independent (different channel)

Flag false corroboration:
```
FALSE CORROBORATION:
  competitive/competitor-posture.md — marked "corroborated" but both sources trace to the same person (Jane, discovery call + Jane, follow-up call). Downgrade to uncorroborated.
```

### Check 3: Contradiction Detection

Cross-reference wiki pages for internal contradictions:
- Page A says "50 applications" but Page B says "47 applications" — which is right?
- Page A says "uses Competitor X" but Page B says "evaluating Competitor X" — different claim
- Stakeholder page says "CISO is champion" but meeting notes suggest skepticism

```
CONTRADICTIONS:
  architecture/app-inventory.md says "50 apps" vs architecture/scale-profile.md says "47 apps"
  → Likely: 47 is from scan data (higher trust), 50 is from verbal estimate. Recommend updating to 47.
```

### Check 4: Poisoning Vectors

Check the two main corruption paths:

**Human poisoning:** Find facts that entered via transcription/verbal source AND have no independent verification AND are used in quantitative recommendations (business cases, sizing, timelines).
```
POISON RISK (human):
  value-drivers/ticket-volume.md — "5,000 tickets/month" entered from a single call transcript.
  Used in business case to calculate $420K/year savings. If this number is wrong, the business case is wrong.
  → Verify with the source or cross-check against benchmark ratios.
```

**Skill poisoning:** Find facts that entered via automated research skills AND contradict other sources or seem implausible.
```
POISON RISK (skill):
  competitive/competitor-posture.md — automated research says "Competitor deployed 18 months ago"
  but this contradicts job posting data showing they're still hiring for the initial rollout.
  → Flag as [CONFLICTING DATA]. Needs human verification.
```

### Check 5: Staleness Cascade

A stale fact that other facts depend on creates a cascade:
- If Page A is stale, and Pages B, C, D cite Page A as a source, then B, C, D are all suspect.
- Map the dependency chain. Flag the root stale page AND all downstream dependents.

```
STALENESS CASCADE:
  ROOT: architecture/current-state.md (stale: 95 days, threshold 90d)
  DOWNSTREAM (3 pages depend on this):
    - competitive/competitor-posture.md — competitive positioning assumes current architecture
    - value-drivers/license-consolidation.md — savings calculation assumes current tool inventory
    - stakeholders/it-director.md — stance assessment assumes current pain points
  → Refreshing the root page may invalidate all 3 downstream pages.
```

### Output Structure

```markdown
# Knowledge Audit: <Target>
Date: <YYYY-MM-DD>

## Summary
- Pages audited: N
- Critical findings: N
- Warnings: N
- Info: N
- Estimated poisoning risk: HIGH / MEDIUM / LOW

## Critical Findings
<Each finding with severity, affected pages, recommended action>

## Corroboration Health
- Established (3+ sources): N pages (N%)
- Corroborated (2 sources): N pages (N%)
- Uncorroborated (1 source): N pages (N%)
- Inferred (0 sources): N pages (N%)
- False corroboration detected: N pages

## Poisoning Risk Assessment
### Human-Source Risks
<Facts from verbal sources used in quantitative outputs>

### Skill-Source Risks
<Facts from automated research that conflict or seem implausible>

## Staleness Map
<Root stale pages and their downstream cascades>

## Recommended Actions
1. <Highest priority fix — what to verify first>
2. <Second priority>
3. <Third priority>
```

---

## Phase 2: Co-Create

```
Audit complete. Here's what I found.

A) Deep-dive on a specific finding
B) Run verification on a flagged fact (I'll try to independently confirm it)
C) Bulk-downgrade false corroboration (I'll update frontmatter)
D) Save the audit report
E) Other
```

---

## Phase 3: Learn

On save:
1. Write feedback file
2. Log any corrections as learnings (type: correction, confidence: 10)
3. If false corroboration found, log as calibration learning (the system's corroboration tracking has a blind spot)
4. Update activity log: "Knowledge audit: N critical, N warnings, N pages flagged for refresh"
5. Queue wiki updates for any downgraded corroboration levels
