---
name: email-generation
description: >
  Generates personalized outreach emails for Miro XDR contacts, using account research
  and persona classification to tailor every message. Use this skill when a user asks
  to "write emails", "draft outreach", "generate email copy", "create sequences",
  "write messages for this list", or wants personalized first-touch emails for their
  contacts. Requires enriched contacts and account research as inputs. Also use when
  a rep asks to "rewrite" or "adjust" previously generated emails.
---

# Email Generation

You write personalized first-touch outreach emails for each contact in the rep's list.
Every email is tailored to the contact's persona, their account's strategic context,
and their existing Miro usage.

Before starting, read both reference files:
- `references/miro-context.md` — Miro positioning, value props, and feature details
- `references/email-guidelines.md` — Tone, structure, examples, and anti-patterns

## Inputs

This skill needs three things:
1. **Contact list** — a CSV with: first_name, last_name, title, email, account_name,
   persona, and optionally: total_sessions, last_activity, features_used
2. **Account research** — the `account_research_{tag}.md` file from the Account
   Research skill
3. **Miro context** — from the reference file

If account research hasn't been run yet, tell the rep: "I need account research to
write strong emails. Want me to run that first, or should I write more generic
emails based on title alone?" Generic emails are a fallback, not the default.

## Steps

### 1. Plan the Batch

Before writing, scan the contact list and the account research to plan:

```
Email Generation Plan
━━━━━━━━━━━━━━━━━━━━━
Contacts to write for: {count}
Accounts covered: {count}

Angle distribution:
  AI Transformation:     {count} contacts across {X} accounts
  Product Acceleration:  {count} contacts
  Business Transformation: {count} contacts
  Enterprise/IT:         {count} contacts
  General (no research): {count} contacts

Proceeding with email generation...
```

### 2. Select Angle Per Contact

For each contact, choose the email angle by combining:

**A. The account's primary theme** (from account research):
What is this company's biggest signal? AI transformation? Product velocity? Digital
modernization? Use the "Miro Angle" from the account brief as the starting point.

**B. The contact's persona** (from persona classification):
What does this person specifically care about based on their role?

| Persona | They Care About | Miro Angle to Lead With |
|---|---|---|
| Director+ Strategy/Transformation | Strategic outcomes, cross-functional alignment, board-level results | AI transformation, change management at scale |
| Director+ Product | Shipping faster, roadmap alignment, team velocity | Product acceleration, roadmapping, sprint planning |
| Director+ Engineering | Technical architecture, developer experience, AI tooling | MCP integration, technical collaboration, AI workflows |
| IT Admin / Director of IT | Tool consolidation, security, governance, cost | Enterprise Guard, SSO/SCIM, admin controls |
| Manager Product/Engineering | Team productivity, meeting overhead, workshop quality | Templates, async collaboration, Jira integration |
| Manager General | Team efficiency, project visibility, collaboration | Visual workspace, meeting facilitation, async work |

**C. Their Miro usage** (from CSV data):
- High sessions (50+) → Reference that they're a power user
- Recent activity (last 7 days) → Mention timely usage
- MCP feature usage → Lead with AI capabilities
- Low/no activity → Don't reference usage at all; focus on company angle

### 3. Write Each Email

For each contact, generate:

**Subject line:**
- 4-7 words
- Specific to the account or situation — never generic
- Lowercase is fine
- No clickbait, no emojis, no ALL CAPS

**Body:**
- Under 120 words
- Plain text — no bullet points, no bold, no formatting
- One paragraph, two at most
- Structure: Hook → Bridge → Value → Soft CTA
- First name greeting ("Hi {first_name},")
- Sign off with just the rep's first name (the sequence tool adds the full signature)

**The four parts:**

1. **Hook** (1 sentence) — Something specific about their company. Pull from account
   research. Never start with "I hope this finds you well" or "My name is..."

2. **Bridge** (1-2 sentences) — Why you're reaching out to them specifically. Connect
   their role or their team's Miro usage to the company context.

3. **Value** (1-2 sentences) — What Miro enables that's relevant. Reference a specific
   capability, not "better collaboration." Tie to an outcome they'd care about.

4. **CTA** (1 sentence) — A question, not a demand. Something that invites a reply.
   Never "Can we schedule 15 minutes?" on a first touch.

### 4. Present for Review

Show each email to the rep as you write it. Format:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
{First Name} {Last Name}
{Title} · {Account Name}
Persona: {persona} | Angle: {angle chosen}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Subject: {subject line}

{email body}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

After every 5 emails, pause and ask:
"How are these looking? Want me to adjust the tone, make them shorter/longer,
or change the angle for any of these?"

The rep may want to tweak specific emails or give direction that applies to the
rest of the batch. Incorporate their feedback before continuing.

### 5. Email Summary

```
Email Generation Complete
━━━━━━━━━━━━━━━━━━━━━━━━━
Emails drafted: {count}

By angle:
  AI Transformation: {count}
  Product Acceleration: {count}
  Business Transformation: {count}
  Enterprise/IT: {count}

Average length: {word count} words
All emails saved to: email_drafts_{tag}.csv

Ready for → CSV Export
```

## Quality Checks

Before delivering each email, verify:
- [ ] Under 120 words
- [ ] Subject line is 4-7 words and specific
- [ ] Hook references something real about the company (not generic)
- [ ] No "I hope this finds you well" or "My name is" openers
- [ ] CTA is a question, not a meeting request
- [ ] Tone is conversational, not corporate
- [ ] No bullet points or formatting in the body
- [ ] Acknowledges existing Miro usage (if the contact has session data)
- [ ] The Miro value prop is specific to a feature or outcome, not generic "collaboration"
- [ ] No links (first touch — earn the reply first)

## Handling Rewrites

If the rep asks to "rewrite", "adjust", or "change" emails:
- If they give general direction ("make them all shorter", "more casual tone"),
  apply it to all remaining emails
- If they point to a specific email, rewrite just that one
- If they say "I like #3, make the rest more like that", use email #3 as the
  tone/style template

## Reference Files

Read before generating any emails:
- `references/miro-context.md` — Value props, features, positioning
- `references/email-guidelines.md` — Tone, structure, examples, anti-patterns
