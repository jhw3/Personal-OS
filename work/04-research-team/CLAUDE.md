# Research Team

## Type
Automation (on-demand, scheduled)

## Purpose
Given a company or contact name, pulls together background, recent news, and likely pain points as prep material for outreach or a discovery call — feeds FirmaTRUST's BD pipeline. Runs on-demand against a specific target, or weekly against whatever's sitting in the research queue (a plain list the user maintains, same pattern as Market Pulse's competitor watchlist). If the queue is empty, the weekly run reports that cleanly instead of erroring out.

## Entry Points
- On-demand: `/research-team "Company or Contact Name"`
- Scheduled: weekly, Wednesday at 9:00 AM — processes `vault/projects/research-team/queue.md` (see scheduler/schedule.md)

## Tools Used
- WebSearch (company background, recent news, funding/hiring signals)
- Notion MCP (`notion-create-database`, `notion-move-pages`, `notion-update-data-source`, `notion-create-view`, `notion-create-pages`, `notion-query-data-sources`)

## Notion Integration
New database: **"Research Team"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per research run (one company/topic).

**Columns:**
- `Report` (title) — e.g. "Research — Acme Corp — 2026-07-30"
- `Date` (date)
- `Subject` (rich text) — the company or contact researched
- `Type` (select: Account/Prospect, Contact, Topic)
- `Key Finding` (rich text) — one-line most-actionable takeaway (the pain point or hook to use in outreach)

**Views:** Table, sorted by Date descending.

Each row's page `content` holds the full research writeup, not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/research-team/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/research/{subject}.md` — one file per company/contact/topic researched, the actual dense findings.
- **Queue (read, user-maintained):** `vault/projects/research-team/queue.md` — plain list of companies/topics to research on the weekly run.

## Vault Reads
- `soul.md` for voice
- `vault/projects/research-team/queue.md` for the weekly run's targets
- `vault/business/{name}.md` and `vault/people/{name}.md` for any existing context on the subject (avoid re-researching what's already known; note what's new)

## Vault Writes
- New `vault/research/{subject}.md` per subject researched
- Creates/updates `vault/business/{company}.md` for any company researched (prospect/account page)
- Creates/updates `vault/people/{name}.md` for any named contact surfaced in the research
- Updates `vault/projects/research-team/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Personal CRM (account/contact context), Weekly Exec Report (pipeline research summary)
- **Depends on:** the research queue being populated for the weekly run to have something to do — degrades gracefully (reports "queue empty" rather than failing) when it isn't

## Post-Run (mandatory)
1. Create/update `vault/people/{name}.md` for any contact surfaced
2. Create/update `vault/business/{company}.md` for any company researched
3. Add `[[wiki links]]` — research file links to `[[projects/research-team/status]]` and any `[[business/name]]` / `[[people/name]]` pages touched
4. Update Notion: create a row in the Research Team database with full `content`
5. Update `vault/index.md`
6. Update `vault/log.md`
7. On first build only: mark "Research Team" Done on the Progress Tracker board

## Implementation Notes
- On-demand mode takes the argument directly, no queue interaction needed.
- Weekly mode reads `vault/projects/research-team/queue.md`; if it lists real subjects, process each and note in the queue file that they've been researched (leave the file for the user to prune, don't auto-delete entries). If the queue is empty or placeholder-only, skip the WebSearch calls entirely and just report "nothing queued this week" rather than running a generic filler search — this automation has no useful default target the way Market Pulse's category scan does.
- Cap each research writeup to what's actually actionable: company background, one or two recent signals, a likely pain point, and a suggested angle — not a company wiki dump.
- If WebSearch is unavailable, skip gracefully and note it rather than failing the run.

## Built (2026-07-30, first run)
- Notion database "Research Team" created directly under the Personal OS parent page with select options intact. DB ID and data source ID in `vault/projects/research-team/status.md`.
- View: "All Research" (table, sorted by Date descending).
- `vault/projects/research-team/queue.md` created empty (same pattern as Market Pulse's watchlist).
- First-run test covered the weekly empty-queue path only: confirmed no WebSearch fires and the run reports "nothing queued" cleanly, per spec (no generic filler search, unlike Market Pulse). No real prospect name was available to test on-demand mode — that path is built but unverified until run against an actual target.
- Marked "Research Team" Done on the Progress Tracker board (4/10).
