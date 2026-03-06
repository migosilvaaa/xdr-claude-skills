---
name: persona-classification
description: >
  Classifies contacts in a Miro XDR pipeline into persona categories based on their
  job title, then filters out ICs and low-priority contacts. Use this skill when a
  user asks to "classify contacts", "sort by persona", "filter ICs", "segment this
  list", "who should I target", "prioritize contacts", "remove ICs", or wants to
  understand which contacts to focus outreach on. Typically runs after Contact
  Enrichment and before Email Generation. Ensures the rep only spends time on
  Director+, Manager, and IT Admin personas.
---

# Persona Classification

You classify each contact into a persona bucket based on their job title, then filter
the list so the rep focuses outreach on the right people. This replaces the manual
process of scanning titles one by one and deciding who to include.

## Input

A CSV with at minimum: `first_name`, `last_name`, `title`, `email`, `account_name`

Typically the output of Contact Enrichment (titles should be filled in by this point).

## Persona Definitions

Classify each contact into exactly one of these buckets:

### Tier 1 — Primary Targets (always include)

**Director+**
Titles that match: Director, Senior Director, VP, SVP, EVP, Chief (CTO, CIO, CFO,
COO, CDO, CPO, CISO, CXO), Head of, President, GM, General Manager, Managing Director,
Principal (when in a leadership context)

These are the decision-makers and budget-holders. They're the primary outreach targets.

**IT Admin**
Titles that match: IT Administrator, System Administrator, Sysadmin, IT Manager,
Director of IT, IT Operations, Infrastructure Manager, Enterprise Admin, Workspace Admin

These control tool deployment, SSO, and security — critical for enterprise deals.

### Tier 2 — Secondary Targets (include by default, rep can opt out)

**Manager**
Titles that match: Manager, Senior Manager, Team Lead, Group Lead, Program Manager,
Project Manager, Engineering Manager, Product Manager, Design Manager

Managers are influencers. They champion tools within their teams and can drive
bottom-up adoption.

### Tier 3 — Deprioritize (exclude by default, rep can opt in)

**IC (Individual Contributor)**
Titles that match: Engineer, Developer, Designer, Analyst, Coordinator, Specialist,
Associate, Consultant (without "Senior" or "Principal" prefix that suggests leadership),
Intern, Fellow, Researcher, Writer, Marketer

ICs are end users but typically don't drive purchasing decisions. Exclude by default.

### Unclassified

Contacts with blank, "Unknown", or ambiguous titles that don't clearly fit any bucket.

## Steps

### 1. Classify Each Contact

Go through every contact and assign a persona based on their title. Use these rules:

- Match against the title patterns above. Be case-insensitive.
- If a title could fit multiple buckets, use the **highest** tier. Example: "IT
  Director" is Director+, not IT Admin.
- If a title is ambiguous (e.g., "Lead" without context), classify as Manager.
- Titles like "Founder" or "Owner" go in Director+ (they're decision-makers).
- "Senior Engineer" or "Staff Engineer" are still IC unless followed by "Manager"
  or "Lead".

### 2. Handle Edge Cases

**Ambiguous titles — ask when unsure:**
If more than 3 contacts have titles you can't confidently classify, show them to
the rep and ask:

```
These titles are ambiguous — how should I classify them?
  - "Technical Lead" → Manager or IC?
  - "Solutions Architect" → IC or Manager?
  - "Practice Lead" → Manager or Director+?
```

**Unknown titles:**
Put contacts with blank or "Unknown" titles in the Unclassified bucket. Recommend
the rep either:
- Try to re-enrich these (run Contact Enrichment again)
- Manually review the short list

### 3. Present the Classification

Show the full breakdown:

```
Persona Classification
━━━━━━━━━━━━━━━━━━━━━━

TIER 1 — Primary Targets
  Director+:  {count} contacts
  IT Admin:   {count} contacts

TIER 2 — Secondary Targets
  Manager:    {count} contacts

TIER 3 — Excluded
  IC:         {count} contacts (will be removed)

Unclassified: {count} contacts (need review)

━━━━━━━━━━━━━━━━━━━━━━

Default action: Include Tier 1 + Tier 2, exclude Tier 3.
This gives you {count} contacts for outreach.

Want to adjust? You can:
  - Include ICs (say "keep ICs")
  - Exclude Managers (say "directors only")
  - Review unclassified (say "show unclassified")
```

### 4. Show the Filtered List

After the rep confirms (or accepts the default), show the outreach-ready list:

```
Outreach List ({count} contacts)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Director+ ({count}):
  {Name} — {Title} — {Account}
  {Name} — {Title} — {Account}
  ...

IT Admin ({count}):
  {Name} — {Title} — {Account}
  ...

Manager ({count}):
  {Name} — {Title} — {Account}
  ...
```

If the list is long (30+ contacts), show the first few per bucket and note the total.

### 5. Add Persona Column

Add a `persona` column to the dataset with values: `director_plus`, `it_admin`,
`manager`, `ic`, or `unclassified`.

Also add a `persona_tier` column: `1`, `2`, `3`, or `unclassified`.

### 6. Save Output

Save the classified and filtered list as: `classified_{tag}.csv`

This should include all contacts (even ICs) but with the persona columns added,
so nothing is permanently lost. The downstream Email Generation skill will use
the persona field to select the right messaging angle.

Also note which contacts were excluded so the rep can see them if needed.

```
Classification Complete
━━━━━━━━━━━━━━━━━━━━━━━
Outreach list: {count} contacts (Tier 1 + Tier 2)
Excluded: {count} ICs
Unclassified: {count} (recommend manual review)

Saved to: classified_{tag}.csv

Ready for → Email Generation
```

## Classification Accuracy

- Precision matters more than recall here — it's worse to wrongly include an IC
  than to wrongly exclude a Director.
- When in doubt about a title, classify one tier lower and flag it. Let the rep
  make the call.
- Common tricky titles and how to handle them:
  - "Technical Program Manager" → Manager
  - "Principal Engineer" → IC (unless "Principal" + leadership context)
  - "Chief of Staff" → Director+
  - "Scrum Master" → Manager
  - "Architect" / "Solutions Architect" → IC (unless "Chief Architect" → Director+)
  - "Fellow" → IC
  - "Evangelist" → IC
  - "Partner" → Director+
