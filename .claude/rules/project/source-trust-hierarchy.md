# Source Trust Hierarchy — All Agents

Not all sources are equal. This hierarchy defines what agents can trust, what requires verification, and what must NEVER be assumed true.

## The Iron Rule

**If it's not in an official doc, don't claim it as fact.** Period.

Training data, marketing materials, internal slides, and even statements from colleagues can be wrong, outdated, or aspirational. Only official documentation is ground truth for product capabilities.

## Verification-First (All Agents)

Before claiming ANY product capability in any output, verify it against official docs:

1. Query documentation tools (Context7 MCP, official doc sites) with the specific capability question
2. **Citation format:** `[VERIFIED — docs]` when confirmed, `[VERIFIED — web]` when confirmed via web search, `[UNVERIFIED — docs unavailable]` when neither works
3. **Minimum requirement:** Every output that references product capabilities should have at least 3 doc-verified citations

## Trust Tiers

### Tier 1: TRUST (cite directly)

| Source | Trust Level |
|--------|-------------|
| **Official product documentation** | Highest — this IS the product |
| **SEC filings** (10-K, 10-Q, proxy statements) | Highest — legally audited |
| **Published analyst reports** (Gartner MQ, Forrester Wave — with date) | High — cite the specific report + date |
| **Official competitor documentation** | High — verify they're current |

### Tier 2: VERIFY BEFORE CITING

| Source | Risk | Action |
|--------|------|--------|
| **Marketing materials** (website, pitch decks, brochures) | May overstate capabilities, use superlatives, omit limitations | Cross-reference against Tier 1 docs. Strip marketing language. Flag: `[MARKETING CLAIM — verify against docs]` |
| **Internal documents** (frameworks, battlecards, proposals) | May contain aspirational positioning, outdated claims | Treat as guidance for FRAMING, not as source of FACTS. Flag: `[INTERNAL CLAIM — verify against docs]` |
| **Internal Slack/email** | Individual opinions, may not reflect official position | Flag: `[UNVERIFIED — INTERNAL SOURCE]` |
| **Blog posts** | More accurate than marketing but may lag reality | Cross-reference against docs. Note the date. |
| **Customer case studies** | Curated, may omit challenges | Cite specific metrics. Flag qualitative claims as `[VENDOR CASE STUDY]` |
| **Unclassified ingested documents** | May be proposals not agreed, optimistic assessments, one person's interpretation | Frame as claims: "Document states X" not "X is the case." Tag: `[UNCORROBORATED — single source: <author or filename>]` |

**The overarching principle for ingested documents:** A document existing in your inbox proves someone wrote it. It does not prove anyone agreed, the numbers are accurate, the proposal was accepted, the strategy worked, or the information is current. Frame all ingested content as claims.

### Tier 3: USE WITH SKEPTICISM

| Source | Risk | Action |
|--------|------|--------|
| **Training data / model knowledge** | May be outdated, hallucinated, or wrong | ALWAYS verify against Tier 1 docs. Flag: `[UNVERIFIED — TRAINING DATA]` |
| **Web search results** | May be outdated, biased, or incorrect | Cross-reference. Note the source date. |
| **Competitor marketing** | Competitors overstate too | Cross-reference against THEIR official docs. Be fair. |
| **Community forums** | User-contributed, may be wrong | Useful for signals, not for capability claims. |
| **Job postings** | Signals intent, not confirmed state | Flag as `[INFERRED FROM JOB POSTING]` |

### Tier 4: HANDLE WITH CARE

| Source | Rule |
|--------|------|
| **Product roadmap** | NEVER disclose unless explicitly authorized. Flag: `[ROADMAP — DO NOT DISCLOSE]` |
| **Pre-release / beta features** | Flag: `[PRE-RELEASE — NOT GA]` |
| **Pricing** | NEVER cite specific pricing. Refer to sales. Flag: `[PRICING — REFER TO SALES]` |
| **NDA-protected information** | NEVER include in any output. Flag: `[POSSIBLE NDA — VERIFY BEFORE SHARING]` |
| **PII** | NEVER include real names unless from public sources |

## Human Sources

Not all human sources are equal. Apply the same skepticism to human-stated facts as to any other unverified source.

| Scenario | Trust Level | Corroboration |
|----------|------------|---------------|
| Single human source, no cross-check | MEDIUM | uncorroborated |
| Two independent humans agree | HIGH | corroborated |
| Human contradicted by scan/doc data | Trust scan/doc | Flag discrepancy |
| Human + web research agree | HIGH | corroborated |
| Human + machine scan agree | HIGHEST | established |

**The Iron Rule (Extended):** If it came from a single human and hasn't been independently verified, it's a claim — not a fact. Tag it accordingly.

## Conflict Resolution

```
Tier 1 doc says X, but training data says Y     -> Trust Tier 1. Flag: "[DOCS SAY X]"
Tier 1 doc says X, but internal doc says Y       -> Trust Tier 1. Flag: "[INTERNAL CLAIM CONFLICTS WITH DOCS]"
Two Tier 1 docs disagree                         -> Flag both. "[CONFLICTING DOCS — verify which is current]"
Tier 1 doc is silent (doesn't mention feature)   -> Do NOT assume it doesn't exist. Flag: "[NOT FOUND IN DOCS — verify manually]"
Competitor doc says they have X, your info says no -> Trust THEIR docs. Update your intel.
```

## The Bottom Line

**When in doubt, verify. When you can't verify, flag. When you flag, be specific about WHY you're uncertain.**
