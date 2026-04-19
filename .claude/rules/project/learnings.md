# Learnings System — Cross-Session Intelligence

Structured observations extracted from user corrections, review calibrations, and explicit requests. They compound intelligence across sessions and projects.

## Storage

- **Per-project:** `output/<project>/_learnings.jsonl` — corrections and preferences specific to one project
- **Global:** `output/_learnings/global.jsonl` — cross-project patterns, calibrations

## Schema

One JSONL line per learning:

```json
{
  "v": 1,
  "ts": "<ISO-8601>",
  "agent": "<agent>",
  "skill": "<skill>",
  "project": "<project-or-null>",
  "type": "<type>",
  "key": "<unique-key>",
  "insight": "<description>",
  "confidence": 8,
  "source": "<source>",
  "scope": "project|global"
}
```

### Types

| Type | Scope | When |
|------|-------|------|
| `correction` | Per-project or global | User corrects a factual claim |
| `preference` | Per-project | User expresses format/approach preference |
| `calibration` | Always global | Skill consistently over/underestimates |
| `pattern` | Always global | Recurring observation across projects |
| `competitive` | Always global | Competitive intelligence |
| `product` | Always global | Product capability correction |

### Confidence Scale

| Score | Meaning |
|-------|---------|
| 10 | User explicitly stated or corrected |
| 8-9 | Verified against docs or scan data |
| 6-7 | Observed pattern, 2+ occurrences |
| 4-5 | Single observation, reasonable inference |
| 1-3 | Speculative, low evidence |

## Read Path

At skill start, search learnings:
1. Per-project: all entries
2. Global: filtered by relevance (same skill, same agent, same type)
3. Cap at 5 learnings, sorted by confidence desc then recency desc
4. Flag stale learnings per staleness thresholds

Display:
```
LEARNINGS (N relevant[, M stale]):
  * [type, confidence N] insight (source, date)
  * [type, confidence N, STALE] insight (source, date)
```

## Write Path

### Auto-Extraction from Feedback

After writing the feedback file, extract learnings from each section:

| Feedback Section | Learning Type | Confidence |
|---|---|---|
| Corrections | `correction` | 10 |
| Expansions | `preference` or `pattern` | 8 |
| Disagreements | `preference` | 10 |
| Format Preferences | `preference` | 10 |
| User-Added Context | `correction` or `pattern` | 8 |
| Cuts | No learning | — |

### Deduplication

Before writing, search the target file for same `key`:
- Same insight, same or lower confidence -> skip
- Same key, different insight -> update
- Same key, higher confidence -> update confidence

### Review Calibration

When adversarial review returns failures and fixes are applied:
- Log `type: calibration` for each failing criterion
- Start at confidence 7, bump by 1 on repeats, cap at 9

### Explicit User Capture

When user says "remember that..." or "note that...", write directly. Confidence 10, source user-stated.

## Staleness

| Type | Stale After |
|------|-------------|
| `correction` | 90 days |
| `preference` | 180 days |
| `calibration` | 180 days |
| `pattern` | 365 days |
| `competitive` | 90 days |
| `product` | 90 days |

Stale learnings are still surfaced but flagged. Never auto-deleted.

## Consolidation

Triggers at 50 entries (per-project) or 100 entries (global):
1. Remove duplicates (same key, keep highest confidence + most recent)
2. Remove contradicted entries (newer supersedes older on same key)
3. Upgrade confidence on patterns observed 3+ times
4. Archive originals
