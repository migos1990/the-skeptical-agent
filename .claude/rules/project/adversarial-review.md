# Adversarial Review Gate — Santa Method

After ANY skill produces its final output, run a multi-model adversarial review before presenting it to the user. Make a list, check it twice. If it's naughty, fix it until it's nice.

## Why

A single agent reviewing its own output shares the same biases, knowledge gaps, and systematic errors that produced the output. Two+ independent reviewers with no shared context break this failure mode.

## When to Run

- Every output that will be shared externally or used for decisions
- Every output that makes factual claims, cites statistics, or references product capabilities
- `--quick` mode does NOT bypass the review
- Team mode does NOT bypass the review

## Exemptions

Do NOT run on:
- System utilities (file cleanup, state management, configuration)
- The review itself (no recursion)
- Ephemeral outputs (daily dashboards, status checks)

## Review Criteria

Send the output to 2-3 independent challenger models. Each evaluates against this rubric:

1. **Factual Accuracy** — All claims verifiable, no fabricated data, no invented statistics
2. **Source Citations** — Every quantitative claim has a named source with date
3. **Logical Consistency** — Conclusions follow from evidence, no internal contradictions
4. **Vendor Objectivity** — No marketing language, limitations acknowledged, balanced perspective
5. **Required Structure** — Output follows expected structure. Does NOT penalize brevity — short-but-substantive is a pass.
6. **Epistemic Honesty** — Uncertainty flagged with markers, fact vs inference distinguished
7. **Sharpness** — Direct, specific, free of filler. No marketing language ("industry-leading", "best-in-class"). No hedge-everything syndrome. No vendor cheerleading.
8. **Corroboration Integrity** — Material facts from sources carry corroboration tags (`[UNCORROBORATED]`, `[CORROBORATED]`, `[INFERRED]`). No single-source claim presented as established fact without a tag.
9. **Document-Claim Framing** — Facts sourced from ingested documents use claim-framing language ("document states X") rather than assertion language ("X is true").

## Verdict Gate

**PASS (NICE):** ALL reviewers pass ALL criteria. Ship it.

**FAIL (NAUGHTY):** ANY reviewer fails ANY criterion. Fix cycle begins.

## Fix Cycle

1. Collect all failed criteria and their specific issues from all reviewers
2. Deduplicate overlapping issues
3. Apply targeted fixes — ONLY what failed criteria require. Do not alter passing sections.
4. Re-run ALL reviewers on the fixed output (fresh context — no memory of Round 1)
5. Maximum 2 rounds. If still failing, escalate to the human.

## Challenger Prompt Template

```
You are a skeptical reviewer. Your job is to find problems, not to approve.

Evaluate against this rubric. For each criterion, return PASS or FAIL with specific issues.

CRITERIA:
[paste the 9 criteria above]

Return JSON:
{
  "verdict": "PASS or FAIL",
  "criteria": [
    {"name": "...", "result": "PASS or FAIL", "issues": ["..."]}
  ],
  "critical_issues": ["blocking issues that must be fixed"],
  "suggestions": ["non-blocking improvements"]
}

Set verdict to FAIL if ANY criterion has result FAIL.

DOCUMENT:
<paste output here>
```

## Implementation

### Option A: Multi-Model via API (Recommended)

Send to 2-3 different model families via LiteLLM or direct API calls. Model diversity catches more blind spots than multiple calls to the same model.

### Option B: Claude Code Subagents

Spawn 2 review agents in parallel using the Agent tool. Each gets the same rubric, neither sees the other's assessment.

### Option C: Sequential Inline (Fallback)

When subagents aren't available, simulate with explicit context resets between reviews. Subagent pattern is strictly superior.

## Output Format

Append a review section at the bottom of the output:

```markdown
## Review
- Verdict: NICE/NAUGHTY (ESCALATED)
- Reviewers: [model list]
- Round: [1 or 2]
- [Pass/fail table: criteria x reviewers]
- Fixes applied: [if Round 2]
- Remaining issues: [if escalated]
```

## Error Handling

- **API key not set:** Skip review, warn user, mark output `[UNREVIEWED]`
- **One model fails:** Continue with remaining. Note reduced confidence.
- **All models fail:** Skip review, warn user.
- **Model refuses adversary role:** Rephrase as "review for accuracy." If still refuses, skip that model.
