# Weekly Exec Report

## Type
Automation (scheduled, on-demand)

## Purpose
The capstone. Every Friday, rolls up what happened across all 9 other automations that week into a single-page PDF brief — sprint board progress, pipeline (Personal CRM), meetings, market signal (Market Pulse), research, inbox health (Email Triage), subscription spend (Expense Wrangler), content drafted (Content Machine). Read it in two minutes instead of opening nine Notion databases.

## Entry Points
- Scheduled: weekly, Friday at 4:00 PM (see scheduler/schedule.md)
- On-demand: `/weekly-exec-report`

## Tools Used
- Notion MCP (`notion-query-data-sources` against every other automation's data source, plus create/update for this automation's own database)
- Python (reportlab) for the PDF, styled per `brand/config/brand-config.md`

## Notion Integration
New database: **"Weekly Exec Report"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per week.

**Columns:**
- `Report` (title) — e.g. "Exec Report — Week of 2026-07-27"
- `Date` (date)
- `Sprint Progress` (rich text) — e.g. "10/10 Done"
- `Pipeline` (rich text) — Personal CRM stage counts
- `Top Signal` (rich text) — the single most important thing from the week (market finding, overdue follow-up, whatever's biggest)

**Views:** Table, sorted by Date descending.

Each row's `content` mirrors the full PDF brief.

## Vault Structure
- **Tier 1:** `vault/projects/weekly-exec-report/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/projects/weekly-exec-report/reports/YYYY-MM-DD.md` — one file per week, source content for the PDF.

## Vault Reads
- `soul.md` for voice
- Every other automation's `vault/projects/{name}/status.md` for its latest state (Sprint Tracker, Morning Brief, Market Pulse, Research Team, Personal CRM, Meeting Intel, Email Triage, Expense Wrangler, Content Machine)
- Live Notion query against each automation's data source for anything not yet reflected in its vault status file
- `brand/config/brand-config.md` for PDF styling

## Vault Writes
- New `vault/projects/weekly-exec-report/reports/YYYY-MM-DD.md` each run
- Updates `vault/projects/weekly-exec-report/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** nothing — this is the terminal rollup
- **Depends on:** all 9 other automations having run at least once; degrades gracefully per-section (a section with nothing to report says so — "no research run this week" — rather than being omitted silently or fabricated)

## Post-Run (mandatory)
1. Update Notion: create this week's row with full `content`
2. Update `vault/index.md`
3. Update `vault/log.md`
4. On first build only: mark "Weekly Exec Report" Done on the Progress Tracker board (this is the 10th and final automation — marking it Done completes the board)

## Implementation Notes
- Pull from each automation's `vault/projects/{name}/status.md` first (fast, already-summarized); only hit Notion live if the vault status looks stale or a section needs a number the status file doesn't have (e.g. current Personal CRM stage counts).
- Every section must trace to something real. A quiet week for a given automation ("no meetings," "queue empty," "$0 spend") is a valid, honest line — never invent activity to fill a section.
- PDF: single page, Steel Blue header bar with logo, Montserrat-style headings, saved to `outputs/weekly-exec-report/YYYY-MM-DD/exec-report.pdf`. Built with reportlab (installed for this automation — wasn't previously available in this environment).
- If a downstream automation's Notion database is unreachable, use its vault status.md snapshot instead and note the section may be slightly stale, rather than failing the whole report.

## Built (2026-07-30, first run)
- Notion database "Weekly Exec Report" created directly under the Personal OS parent page. DB ID and data source ID in `vault/projects/weekly-exec-report/status.md`.
- View: "All Reports" (table, sorted by Date descending).
- reportlab wasn't installed in this environment — installed it for this automation. LibreOffice-based PDF preview (`pdftoppm`) also isn't available, so the rendered PDF was verified by extracting its text back out (`pypdf`) and reading it, not a visual render.
- First draft ran 2 pages and had garbled `&mdash;` entities; fixed by rewriting without em dashes (per soul.md's voice rule, which had been missed throughout this session's earlier automations) and tightening spacing to fit one page.
- First report rolled up real state from all 9 other automations as of 2026-07-30: 9/10 Done pre-completion, $0 confirmed subscription spend, 61 threads triaged, one real market signal, three content drafts, zero contacts/meetings/research (honestly reported as gaps, not filled in).
- Marked "Weekly Exec Report" Done on the Progress Tracker board — the 10th and final automation. Board confirmed 10/10 via live Notion query.
