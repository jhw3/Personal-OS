# Expense Wrangler

## Type
Automation (scheduled, on-demand)

## Purpose
Scans Gmail for SaaS/tool subscription and trial signals (started, ending, ended, upgrade-abandoned) and tracks them — cost when known, status, renewal/trial-end dates — so nothing silently converts from a free trial to a paid charge unnoticed. Outputs to a Notion log plus a running Excel workbook with real formulas. Does not track general receipts/invoices — subscriptions only.

## Entry Points
- Scheduled: monthly, 1st of the month at 8:00 AM (see scheduler/schedule.md)
- On-demand: `/expense-wrangler`

## Tools Used
- Gmail MCP (`search_threads`, `get_thread` for full body when a snippet doesn't confirm pricing/dates)
- Notion MCP (create/update/query tools for this automation's database)
- `/xlsx` skill (brand-styled workbook, real formulas, no hardcoded totals)

## Notion Integration
New database: **"Expense Wrangler"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per subscription (updated in place as its status changes, not one row per email).

**Columns:**
- `Subscription` (title) — e.g. "Slack Pro"
- `Monthly Cost` (number, dollar format) — 0 if free/trial/not confirmed
- `Status` (select: Active, Trial, Trial Ending Soon, Trial Ended, Not Subscribed, Cancelled)
- `Renewal/Trial End` (date)
- `Last Signal` (date) — date of the most recent email that informed this row
- `Cost Confirmed` (checkbox) — true only if an actual dollar amount was read from an email; false means the cost field is a placeholder/unconfirmed

**Views:** Table, sorted by `Renewal/Trial End` ascending (soonest first).

Each row's page `content` holds the evidence — which email(s) this status came from — not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/expense-wrangler/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/projects/expense-wrangler/scans/YYYY-MM.md` — one file per monthly scan.

## Vault Reads
- `soul.md` for voice
- `brand/config/brand-config.md` for the Excel workbook's styling

## Vault Writes
- New `vault/projects/expense-wrangler/scans/YYYY-MM.md` each run
- Updates `vault/projects/expense-wrangler/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Weekly Exec Report (subscription spend line)
- **Depends on:** Gmail MCP for billing/trial signal; degrades gracefully (reports "no new subscription signal since last scan" rather than failing) once the backlog is worked through

## Post-Run (mandatory)
1. Update Notion rows for every subscription with a new signal this run
2. Update `vault/index.md`
3. Update `vault/log.md`
4. On first build only: mark "Expense Wrangler" Done on the Progress Tracker board

## Implementation Notes
- Search terms: sender/subject patterns for trial and billing language (`trial`, `subscription`, `plan`, `billing`, `receipt`, `invoice`) combined with known SaaS sender domains as they're discovered.
- **Never fabricate a cost.** If an email doesn't state a dollar figure, leave `Monthly Cost` at 0 and `Cost Confirmed` unchecked — do not fill in a plan's public list price as if it were confirmed billing. Note the gap in the row's content instead.
- A subscription with no billing/renewal email at all (e.g. pure onboarding drip with no trial-start or trial-end language) isn't tracked as a row — there's no subscription signal to log, just marketing.
- The Excel workbook (`outputs/expense-wrangler/YYYY-MM-DD/subscriptions.xlsx`) mirrors the Notion rows with real formulas: `=SUM` for total monthly cost, a count of trials ending within 14 days. Regenerated each run, not hand-edited.
- If Gmail MCP is unavailable, skip gracefully and note it rather than failing the run.

## Built (2026-07-30, first run)
- Notion database "Expense Wrangler" created directly under the Personal OS parent page with select options intact. DB ID and data source ID in `vault/projects/expense-wrangler/status.md`.
- View: "All Subscriptions" (table, sorted by Renewal/Trial End ascending).
- First run fully exercised against real inbox signal — pulled full email bodies via `get_thread` for the Slack Pro trial-start and Cursor abandoned-upgrade emails to confirm real dates rather than guessing from snippets; two other `get_thread` calls (Zapier, Google Cloud) hit the tool's token limit and fell back to snippet-level detail instead of full body.
- Found 4 subscriptions, all logged at $0 confirmed cost: Slack Pro (trial ended, reverted to free), Zapier Professional (trial ended), Google Cloud (trial still active, end date unconfirmed), Cursor Pro (never subscribed). No public list price was substituted for a real confirmed cost anywhere.
- Generated `outputs/expense-wrangler/2026-07-30/subscriptions.xlsx` via openpyxl directly (brand Steel Blue header, real `=SUM`/`=COUNTIF` formulas). Initially shipped without running the xlsx skill's `recalc.py` verification (LibreOffice wasn't installed yet). **Update, same day:** LibreOffice and Poppler installed, a Windows-compatibility bug in the xlsx skill's `recalc.py` (AF_UNIX socket check) got patched, and this workbook was re-verified: 0 errors, 2 formulas, values confirmed correct (Total Confirmed Monthly Cost = $0, Active Trials = 3). No longer a known gap.
- Marked "Expense Wrangler" Done on the Progress Tracker board (8/10).
