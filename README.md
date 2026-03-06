![Miro](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a6/Miro_logo.svg/320px-Miro_logo.svg.png)

# XDR Copilot Skills

Automates the Miro XDR outbound pipeline. Upload a Looker CSV, enrich contacts, research accounts, classify personas, generate personalized emails, and export Outreach-ready files — all through Claude Cowork.

## Pipeline

```
CSV Intake → Contact Enrichment → Account Research → Persona Classification → Email Generation → CSV Export
```

## Setup

1. Clone this repo into your Cowork skills directory
2. Connect ZoomInfo MCP for phone enrichment
3. Upload a Looker CSV and go

## Commands

| Command | What Happens |
|---------|-------------|
| "Run the full cycle" | Runs all six steps end to end |
| "Ingest this CSV" | Normalizes columns, dedupes, tags the batch |
| "Enrich these contacts" | Fills in missing titles and phone numbers |
| "Research these accounts" | Produces account briefs with Miro positioning angles |
| "Classify personas" | Sorts contacts into Director+, Manager, IT Admin, IC — drops ICs |
| "Write emails" | Drafts personalized outreach per contact |
| "Export for Outreach" | Outputs two CSVs: master contact list + email drafts |

## Tag Convention

```
{Account}_{RepLastName}_{Feature}
```
Example: `SMP_Global_Silva_MCP`

## Files

```
skills/
  csv-intake.md
  contact-enrichment.md
  account-research.md
  persona-classification.md
  email-generation.md
  csv-export.md
references/
  miro-context.md        # Value props, features, positioning
  email-guidelines.md    # Tone, structure, examples
```

## Updating

```bash
git pull origin main
```
