---
name: account-research
description: >
  Researches companies in a Miro XDR's contact list to find AI transformation signals,
  recent strategic initiatives, and pain points — then generates a Miro positioning
  angle for each account. Use this skill when a user asks to "research accounts",
  "look up these companies", "do account research", "find out about these accounts",
  "what's going on at {company}", or wants strategic context before outreach. Works
  on any list of company names or a CSV with an account_name column. Typically runs
  after Contact Enrichment and before Email Generation.
---

# Account Research

You research each unique company in the rep's contact list and produce a strategic
brief that helps them personalize outreach. This replaces the manual process of
Googling each company and trying to piece together what they care about.

Before starting, read `references/miro-context.md` for Miro's current positioning
and value props.

## Input

Either:
- A CSV file with an `account_name` column (typically output from Contact Enrichment)
- A list of company names the rep provides directly

## Steps

### 1. Identify Unique Accounts

Deduplicate the account list. Report:

```
Account Research
━━━━━━━━━━━━━━━━
Unique accounts to research: {count}
  - {account 1} ({X} contacts)
  - {account 2} ({X} contacts)
  - {account 3} ({X} contacts)
  ...

Estimated time: ~{count × 30 seconds} 
Starting research...
```

### 2. Research Each Account

For each company, run these searches:

**Search 1 — Strategic direction:**
`{company_name} AI transformation digital strategy 2025`

**Search 2 — Recent news:**
`{company_name} recent news initiatives announcements`

**Search 3 — Industry context (if needed):**
`{company_name} industry {inferred industry} trends`

Read the most relevant results to extract:
- What industry they're in and approximate size
- Recent strategic initiatives (especially anything related to AI, digital
  transformation, productivity, or business transformation)
- Signals that they're investing in or interested in AI adoption
- Likely pain points that a visual collaboration tool could address
- Any recent leadership changes, reorgs, or major projects

### 3. Generate the Account Brief

For each account, produce a brief using this format:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{ACCOUNT NAME}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Industry:  {industry}
Size:      {approximate employee count or range}
HQ:        {headquarters location if found}

RECENT INITIATIVES
• {initiative 1 — what it is and why it matters}
• {initiative 2}
• {initiative 3 if found}

AI / TRANSFORMATION SIGNALS
• {signal 1 — concrete evidence of AI or transformation interest}
• {signal 2}

LIKELY PAIN POINTS
• {pain point 1 — grounded in what you found, not generic}
• {pain point 2}

MIRO ANGLE
{2-3 sentences: the specific positioning angle the rep should use when
reaching out to contacts at this account. Reference specific Miro capabilities
from the context doc. Be concrete — "Miro can help with X" not "Miro
improves collaboration."}
```

### 4. Progress Updates

After completing each account, give a brief one-liner:

```
✓ {Account Name} — {one sentence summary of the angle}
```

After every 3 accounts, show a running tally: `Completed {X}/{total} accounts`

### 5. Research Summary

After all accounts are done:

```
Account Research Complete
━━━━━━━━━━━━━━━━━━━━━━━━━
Accounts researched: {count}/{total}
  Strong signals found: {count} (clear AI/transformation fit)
  Moderate signals: {count} (some relevant activity)
  Limited info: {count} (not much found — rep should research manually)

Top angles by theme:
  AI Transformation: {list account names}
  Product Acceleration: {list account names}
  Business Transformation: {list account names}
  Enterprise Security/IT: {list account names}

Saved to: account_research_{tag}.md

Ready for → Email Generation
```

### 6. Save Output

Save all briefs to a single file: `account_research_{tag}.md`

This file serves two purposes:
1. The rep reads it to prep for calls and outreach
2. The Email Generation skill reads it to personalize email copy

## Research Quality Standards

- **Be specific, not generic.** "Company X announced a $50M AI initiative in Q3"
  is useful. "Company X is interested in innovation" is not.
- **Cite what you find.** If you reference a specific announcement or initiative,
  note where you found it so the rep can verify.
- **Don't fabricate.** If you can't find meaningful information about a company,
  say so clearly. A brief that says "Limited public information available — recommend
  manual research" is more useful than made-up signals.
- **Connect to Miro.** The angle section is the most important part. Every brief
  should end with a clear, actionable recommendation for how to position Miro.
- **Recency matters.** Prioritize information from the last 12 months. A 2021
  initiative is less useful than a 2025 one.

## Reference Files

Read `references/miro-context.md` before generating any account briefs. It contains:
- The five primary value narratives and when to use each
- Key features and what they do
- The bottoms-up story (these contacts already use Miro)
- Usage signals and what they indicate
