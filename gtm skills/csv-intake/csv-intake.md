---
name: csv-intake
description: >
  Parses and normalizes Looker CSV exports for the Miro XDR outbound pipeline.
  Use this skill whenever a user uploads a CSV file of contacts — whether from
  a Looker territory dashboard, product usage dashboard, or any other contact
  export. Also triggers when users say "process this CSV", "import this list",
  "load contacts", "parse this export", or drop a CSV and ask to get started.
  This is always the first step before enrichment, research, or email generation.
---

# CSV Intake

You are the first step in the Miro XDR outbound pipeline. Your job is to take a
raw CSV export (typically from Looker) and turn it into a clean, normalized dataset
that downstream skills can work with.

## When This Runs

A rep uploads a CSV file — usually exported from one of two Looker dashboards:

1. **Territory Overview** — an account heat map filtered by AE names, showing
   monthly active users per account. The rep clicked into a specific account's
   user list and exported it.
2. **Product Usage Dashboard** — filtered by a specific feature (e.g., MCP, Miro AI),
   showing all contacts using that feature at the account level.

The CSV formats vary, but typically contain some combination of: name, email, title,
account, activity metrics, and feature usage data.

## Steps

### 1. Read the CSV

Read the uploaded file. Handle both comma-delimited and tab-delimited formats.
Handle quoted fields, fields with commas inside quotes, and inconsistent line endings.

If the file cannot be parsed, tell the rep what went wrong and ask them to
re-export or share a few sample rows so you can understand the format.

### 2. Map Columns

Map whatever headers exist to these canonical field names. Be flexible — CSV exports
from Looker are inconsistent.

| Canonical Field | Common Variants to Look For |
|---|---|
| `first_name` | First Name, firstname, first, given_name, First |
| `last_name` | Last Name, lastname, last, surname, family_name, Last |
| `title` | Title, Job Title, job_title, Job_Title, Position |
| `email` | Email, Email Address, email_address, Email_Address, user_email |
| `phone` | Phone, Phone Number, phone_number, Direct Phone, direct_phone, mobile |
| `account_name` | Account Name, Company, company, account, Account, organization, org |
| `total_sessions` | Total Sessions, sessions, activity_count, Activities, num_activities |
| `last_activity` | Last Activity, last_active, last_activity_date, Last Active, last_seen |
| `features_used` | Features Used, last_used_feature, feature, Feature, product_feature |
| `role` | Role, user_role, User Role, miro_role |

If a column doesn't match any canonical field, preserve it as-is — it may be useful
later.

If critical columns are missing (no name fields, no email), flag this to the rep
before proceeding.

### 3. Clean the Data

- Trim whitespace from all fields
- Normalize email addresses to lowercase
- Remove fully duplicate rows (same email address)
- Remove rows with no email address (these are unusable for outreach)
- Standardize date formats in `last_activity` to YYYY-MM-DD where possible
- If `first_name` and `last_name` are combined in a single "Name" column, split them

### 4. Tag the Batch

Ask the rep if they have a tag name for this batch. Explain the naming convention:

> `{AccountOrSegment}_{RepLastName}_{Feature or Sequence}`
> Example: `SMP_Global_Silva_MCP`

If they provide one, use it. If they say "just pick one", auto-generate from:
`{most common account name}_{today's date as MMDD}`

Store the tag — it will be used in filenames and as a column in the final export.

### 5. Report Summary

After processing, present a clear summary:

```
CSV Intake Complete
━━━━━━━━━━━━━━━━━━
Rows loaded:      {total raw rows}
Duplicates removed: {count}
No email (removed): {count}
Clean contacts:   {final count}

Accounts: {number of unique accounts}
Top accounts:
  - {account 1} ({count} contacts)
  - {account 2} ({count} contacts)
  - {account 3} ({count} contacts)
  ...

Field coverage:
  - Title:     {X}/{total} filled ({pct}%)
  - Phone:     {X}/{total} filled ({pct}%)
  - Sessions:  {X}/{total} filled ({pct}%)

Batch tag: {tag}

Ready for → Contact Enrichment
```

### 6. Save Working Data

Save the normalized data as a CSV file: `intake_{tag}.csv`

This file is the input for the next pipeline step (Contact Enrichment).

## Edge Cases

- **Single-column CSV or malformed file**: Ask the rep to check the export and try
  again. Show them the first 3 rows so they can see what you're seeing.
- **Huge file (500+ rows)**: Note the count and ask if they want to process all
  contacts or filter to a specific account or role first.
- **No account name column**: Check if the email domain can be used to infer the
  account. Ask the rep to confirm.
- **Mixed accounts in one CSV**: This is normal for territory exports. Note the
  account distribution in the summary.
