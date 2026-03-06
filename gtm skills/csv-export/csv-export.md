---
name: csv-export
description: >
  Packages the final enriched, classified, and email-drafted Miro XDR contact data
  into clean CSV files ready for import into Outreach. Use this skill when a user
  asks to "export", "create the CSV", "package this up", "get the final file",
  "export for Outreach", "give me the spreadsheet", or is at the end of the XDR
  pipeline and needs downloadable output files. Produces one master CSV for Outreach
  import and optionally a separate email drafts CSV.
---

# CSV Export

You package all the pipeline output into clean, Outreach-ready CSV files. This is the
final step — the rep takes these files and imports them directly into Outreach to
start sequences.

## Inputs

Collect all available data from the pipeline:
- Contact data (from CSV Intake + Contact Enrichment)
- Persona classification (from Persona Classification)
- Email drafts (from Email Generation)
- Account research (from Account Research — referenced but not exported in the CSV)
- Batch tag (from CSV Intake)

## Output Files

Produce two CSV files:

### File 1: Master Contact List

**Filename:** `outreach_import_{tag}.csv`

**Columns (in this order):**

| Column | Source | Description |
|---|---|---|
| first_name | Intake | Contact first name |
| last_name | Intake | Contact last name |
| title | Enrichment | Job title (enriched) |
| email | Intake | Email address |
| phone | Enrichment | Direct phone (from ZoomInfo) |
| account_name | Intake | Company name |
| persona | Classification | director_plus, manager, it_admin, ic, unclassified |
| persona_tier | Classification | 1, 2, 3, or unclassified |
| total_sessions | Intake | Miro session count |
| last_activity | Intake | Last active date |
| features_used | Intake | Features used in Miro |
| tag | Intake | Batch tag for Outreach filtering |

**Filtering:**
- By default, only include Tier 1 and Tier 2 contacts (exclude ICs)
- If the rep previously said to include ICs or exclude Managers, respect that
- Ask: "Export all tiers, or just the outreach list (Tier 1 + 2)?"

### File 2: Email Drafts

**Filename:** `email_drafts_{tag}.csv`

**Columns:**

| Column | Description |
|---|---|
| first_name | Contact first name |
| last_name | Contact last name |
| email | Email address |
| account_name | Company name |
| title | Job title |
| persona | Persona classification |
| subject | Email subject line |
| body | Email body text |
| tag | Batch tag |

**Only include contacts that have email drafts generated.**

## Steps

### 1. Pre-Export Check

Before creating files, validate the data:

```
Export Readiness Check
━━━━━━━━━━━━━━━━━━━━━
Contacts in pipeline: {total}
  With title:    {count} ✓
  With phone:    {count}
  With persona:  {count} ✓
  With email draft: {count}

Missing data:
  {count} contacts still missing titles
  {count} contacts missing phone (non-blocking)
  {count} contacts not classified
  {count} contacts without email drafts

Tag: {tag}
```

If any critical data is missing (no titles, no classification), suggest running the
relevant upstream skill before exporting.

If only phones are missing, that's fine — proceed. Phones are nice-to-have.

### 2. Confirm Scope

Ask the rep:

```
Ready to export. Quick confirmations:
1. Tag: "{tag}" — correct?
2. Include: Tier 1 + Tier 2 ({count} contacts) — or adjust?
3. Export both contact list and email drafts? (Y/n)
```

### 3. Generate the Files

Create clean CSV files:
- Properly escape fields that contain commas or quotes
- Use UTF-8 encoding
- No trailing commas or blank rows
- Headers in the first row

Save both files and present them to the rep.

### 4. Export Summary

```
Export Complete
━━━━━━━━━━━━━━

📄 outreach_import_{tag}.csv
   {count} contacts ready for Outreach import
   Columns: first_name, last_name, title, email, phone,
            account_name, persona, persona_tier,
            total_sessions, last_activity, features_used, tag

📄 email_drafts_{tag}.csv
   {count} personalized email drafts
   Columns: first_name, last_name, email, account_name,
            title, persona, subject, body, tag

Import instructions:
1. Open Outreach → Prospects → Import
2. Upload outreach_import_{tag}.csv
3. Map columns to Outreach fields
4. Tag all imports with "{tag}"
5. Bulk sequence using email_drafts_{tag}.csv for copy
```

### 5. Outreach Import Tips

Remind the rep:
- The `tag` column can be used as an Outreach tag for filtering and sequence enrollment
- Email drafts are starting points — they can adjust before sending
- The `persona` column helps them choose the right sequence in Outreach
- If they're enrolling in a pre-built sequence, they mainly need the master contact
  list; the email drafts are for customizing the first step

## Edge Cases

- **Rep wants a single combined file**: Merge both CSVs — add subject and body
  columns to the master contact list. Some rows will have blank subject/body (no
  email drafted for ICs, for example).
- **Rep wants account research in the export**: Add an `account_summary` column
  with the "Miro Angle" text from each account's research brief. Keep it to 1-2
  sentences per row.
- **Rep wants to re-export after edits**: If the rep tweaks emails or reclassifies
  contacts, regenerate the files with updated data.
