---
name: contact-enrichment
description: >
  Enriches Miro XDR contact lists by filling in missing job titles (via web search)
  and phone numbers (via ZoomInfo). Use this skill when a user asks to "enrich
  contacts", "fill in titles", "look up titles", "get phone numbers", "enrich this
  list", "clean up this CSV", or has a contact list with blank title or phone fields.
  Typically runs after CSV Intake and before Account Research. Works on any CSV that
  has first_name, last_name, email, and account_name columns.
---

# Contact Enrichment

You enrich contact records by filling in missing job titles and phone numbers. This
replaces the manual process where reps Google each person one-by-one to find their
LinkedIn profile and title.

## Input

A CSV file (typically the output of CSV Intake) with at minimum:
- `first_name`
- `last_name`
- `email`
- `account_name`

And optionally: `title`, `phone` (which may be blank — that's what you're fixing).

## Steps

### 1. Assess What Needs Enrichment

Scan the dataset and report:

```
Enrichment Scan
━━━━━━━━━━━━━━━
Total contacts: {count}
Missing title:  {count} → will research via web search
Missing phone:  {count} → will look up via ZoomInfo
Already complete: {count} → no action needed
```

Ask the rep: "Ready to start enrichment? This will take roughly {estimate} based
on {count} lookups." Then proceed.

### 2. Title Enrichment (Web Search)

For each contact with a blank or "Unknown" title, search the web to find their
current professional role.

**Search strategy — try in order, stop when you get a confident result:**

1. `{first_name} {last_name} {account_name} LinkedIn`
2. `{first_name} {last_name} {account_name} title`
3. `"{first_name} {last_name}" {email_domain}` (where email_domain is the part after @)

**What to extract:**
- Their current job title at the company in question
- If they've moved companies, note this — the rep may want to skip them

**Confidence tagging:**
After finding a title, tag it internally:
- **high** — Found on LinkedIn or company page, clearly current
- **medium** — Found a mention but may be outdated or ambiguous
- **low** — Best guess based on fragments; flag for rep review

**Progress updates:**
After every 5 contacts, update the rep:
```
Enriched 5/23 titles...
  ✓ Jane Smith → VP of Product (high confidence)
  ✓ Mike Chen → Director of Engineering (high confidence)
  ? Sarah Lee → "Program Manager" (medium — found on old conference page)
  ✗ Tom Brown → No results found
  ✓ Lisa Park → Head of Digital Transformation (high confidence)
```

### 3. Phone Enrichment (ZoomInfo)

For contacts missing phone numbers, use ZoomInfo to look up direct dial numbers.

**How to use ZoomInfo:**
Use the ZoomInfo MCP tools (search for them via tool_search). The typical flow is:
1. Search contacts by name + company
2. Extract the direct phone or mobile number from results

**Search by:**
- First name + last name + company name
- Email address if name search returns too many results

**If ZoomInfo is not connected:**
Tell the rep: "ZoomInfo isn't connected to this workspace. You can enable it in
your MCP settings, or I can skip phone enrichment and proceed with what we have."
Do not block the pipeline on this.

**If ZoomInfo returns no results for a contact:**
Leave the phone field blank and move on. Note it in the summary.

### 4. Flag Anomalies

During enrichment, flag these for the rep's attention:
- **Contact has left the company**: Web search shows they now work elsewhere
- **Title suggests IC role**: If the enriched title is clearly IC-level (e.g.,
  "Software Engineer", "Marketing Coordinator"), flag it — the rep may want to
  filter these out in the Persona Classification step
- **Duplicate people**: If two rows have different emails but appear to be the
  same person at the same company

### 5. Enrichment Summary

When complete, present:

```
Enrichment Complete
━━━━━━━━━━━━━━━━━━━
Titles enriched:   {found}/{attempted} ({pct}%)
  High confidence: {count}
  Medium confidence: {count}
  Low / not found: {count}

Phones enriched:   {found}/{attempted} ({pct}%)

Flags:
  - {count} contacts may have left their company
  - {count} contacts have IC-level titles (filter in next step)
  - {count} possible duplicates

Ready for → Persona Classification or Account Research
```

### 6. Save Output

Save the enriched dataset as: `enriched_{tag}.csv`

Include all original fields plus the enriched `title` and `phone` values.

## Important Notes

- **Rate yourself.** Don't fire off 50 web searches in rapid succession. Pause
  briefly between lookups.
- **Don't guess titles.** If you can't find a confident result, leave it as
  "Unknown" rather than making something up. A wrong title is worse than no title.
- **Preserve original data.** If a title was already filled in from the CSV, don't
  overwrite it with web search results unless the rep asks you to re-verify.
