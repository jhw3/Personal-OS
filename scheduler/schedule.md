# Scheduled Tasks

This file is maintained by the agent. When the user asks to schedule something, add it here.

To activate these schedules: Open Cowork → Schedule sidebar → Create a local task for each entry below.

---

## Active Schedules

<!-- Agent adds entries here when user requests a schedule -->
<!-- Format: -->
<!-- ### Task Name -->
<!-- - Command: /command-name -->
<!-- - Frequency: daily at 8:00 AM (or whatever) -->
<!-- - Description: what it does -->
<!-- - Added: YYYY-MM-DD -->

### Sprint Tracker
- Command: /sprint-tracker
- Frequency: weekdays at 9:00 AM
- Description: Reads the Progress Tracker board, generates a standup (Done/In Progress/To Do counts + velocity), posts to Notion and vault/projects/sprint-tracker/standups/.
- Added: 2026-07-29

### Morning Brief
- Command: /morning-brief
- Frequency: daily at 7:00 AM
- Description: Pulls today's Calendar events, priority/unread Gmail, and the Sprint Tracker board snapshot into one rundown. Posts to Notion and vault/projects/morning-brief/briefs/.
- Added: 2026-07-29

### Market Pulse
- Command: /market-pulse
- Frequency: weekly, Mondays at 9:00 AM
- Description: Scans named competitors from vault/business/competitors/watchlist.md (falls back to generic AI/SMB market search if empty). Posts to Notion and vault/projects/market-pulse/pulses/.
- Added: 2026-07-29

### Research Team
- Command: /research-team
- Frequency: weekly, Wednesdays at 9:00 AM (also available on-demand with a company/contact argument any time)
- Description: Weekly run processes vault/projects/research-team/queue.md (reports cleanly if empty, no filler research). On-demand run researches whatever name is passed in. Posts to Notion and vault/research/.
- Added: 2026-07-30

### Personal CRM
- Command: /personal-crm
- Frequency: weekly, Thursdays at 9:00 AM (also available on-demand with a contact name argument any time)
- Description: Weekly run syncs vault/people/ pages missing a crm_id into the Notion CRM and flags overdue follow-ups. On-demand run adds/updates one named contact.
- Added: 2026-07-30

### Meeting Intel
- Command: /meeting-intel
- Frequency: daily at 6:30 AM, ahead of Morning Brief (also available on-demand with a meeting name argument any time)
- Description: Daily run pre-briefs today's meetings (attendee background from Personal CRM/vault/people/). On-demand run also processes post-meeting notes/action items and pushes follow-ups into Personal CRM.
- Added: 2026-07-30

### Email Triage
- Command: /email-triage
- Frequency: daily at 8:00 AM, after Morning Brief (also available on-demand any time)
- Description: Sorts unread inbox mail into Needs Reply/FYI/Noise Gmail labels via a Triaged marker for idempotency. Categorize-and-label only — no drafts, no sends, no archiving.
- Added: 2026-07-30

### Expense Wrangler
- Command: /expense-wrangler
- Frequency: monthly, 1st of the month at 8:00 AM (also available on-demand any time)
- Description: Scans Gmail for SaaS subscription/trial signals, tracks status and cost (never fabricates a cost from a public list price). Posts to Notion and regenerates outputs/expense-wrangler/subscriptions.xlsx.
- Added: 2026-07-30

### Weekly Exec Report
- Command: /weekly-exec-report
- Frequency: weekly, Fridays at 4:00 PM (also available on-demand)
- Description: Rolls up all 9 other automations into a one-page brand-styled PDF plus a Notion row. Terminal automation, the last of the original 10.
- Added: 2026-07-30

---

## How to Set Up in Cowork

For each entry above:
1. Open Claude Code Desktop (Cowork)
2. Click Schedule in the sidebar
3. Click New task → New local task
4. Name: use the task name above
5. Prompt: use the command above (e.g., "Run /morning-brief")
6. Frequency: match the frequency above
7. Enable "Keep computer awake" in Cowork Settings if you want it to run while you're away
