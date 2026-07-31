# Morning Brief

## Type
Automation (scheduled, on-demand)

## Purpose
Every morning, pulls together the day's meetings (Google Calendar), priority/unread emails (Gmail), and the current Progress Tracker board snapshot (from [[projects/sprint-tracker/status]]) into one readable brief. The point is James opens one page instead of three apps to know what today looks like and what's still outstanding on the build queue.

## Entry Points
- Scheduled: daily at 7:00 AM (see scheduler/schedule.md)
- On-demand: `/morning-brief`

## Tools Used
- Google Calendar MCP (`list_events` or `search_events` for today's date range)
- Gmail MCP (`search_threads` for unread/priority mail)
- Notion MCP (`notion-create-database`, `notion-move-pages`, `notion-update-data-source`, `notion-create-view`, `notion-create-pages`, `notion-query-data-sources`, `notion-update-page`)

## Notion Integration
New database: **"Morning Brief"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per day.

**Columns:**
- `Brief` (title) — e.g. "Brief — 2026-07-30"
- `Date` (date)
- `Meetings` (number) — count of calendar events that day
- `Priority Emails` (number) — count of flagged/unread priority threads
- `Day Load` (select: Light, Normal, Heavy) — derived from meeting count (0-1 = Light, 2-4 = Normal, 5+ = Heavy)
- `Board Snapshot` (text) — e.g. "2 Done / 1 In Progress / 7 To Do"

**Views:** Table, sorted by Date descending (default/newest-first).

Each row's page `content` holds the full readable brief (same content as the vault file), not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/morning-brief/status.md` — Notion IDs, last run, current schema.
- **Tier 2:** `vault/projects/morning-brief/briefs/YYYY-MM-DD.md` — one file per run, full brief.

## Vault Reads
- `soul.md` for voice
- `vault/projects/sprint-tracker/status.md` for the current board snapshot (Done/In Progress/To Do counts) — read live from the Progress Tracker data source, not cached
- `vault/people/` to recognize known contacts in calendar/email (link `[[people/name]]` where a match exists)

## Vault Writes
- New `vault/projects/morning-brief/briefs/YYYY-MM-DD.md` each run
- Updates `vault/projects/morning-brief/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`
- New `vault/people/{name}.md` for any meeting attendee or email sender not already in the vault

## Connections
- **Feeds into:** Weekly Exec Report (rollup of daily briefs across the week)
- **Depends on:** Sprint Tracker's board data (reads the same Progress Tracker data source), Calendar and Gmail MCP access

## Post-Run (mandatory)
1. Create `vault/people/{name}.md` for new meeting attendees or email senders found, if not already present
2. Create `vault/business/` entries for new companies mentioned in calendar/email context, if any
3. Add `[[wiki links]]` — brief file links to `[[projects/morning-brief/status]]` and any `[[people/name]]` matches
4. Update Notion: create today's row in the Morning Brief database with full `content`
5. Update `vault/index.md`
6. Update `vault/log.md`
7. On first build only: mark "Morning Brief" Done on the Progress Tracker board

## Implementation Notes
- Calendar query: today's date range (00:00 to 23:59 local), `timeMin`/`timeMax` in ISO 8601.
- Gmail query: Gmail search syntax, e.g. `is:unread` or `is:important is:unread`, capped to a reasonable count (top 10) to keep the brief scannable.
- If Calendar or Gmail MCP is unavailable or returns an auth error, skip that section gracefully and note it in the brief rather than failing the whole run (per graceful-degradation rule in `/new`).
- Day Load select value is computed from meeting count, not asked of the user.

## Built (2026-07-29, first run)
- Notion database "Morning Brief" created directly under the Personal OS parent page (select options for Day Load came through on creation this time, no ALTER COLUMN needed). DB ID and data source ID in `vault/projects/morning-brief/status.md`.
- View: "All Briefs" (table, sorted by Date descending).
- First run confirmed both MCPs work: `is:unread is:important in:inbox` correctly returned zero (mailbox has ~200 unread but all newsletter/promo noise, no false positives); Calendar returned zero events for a genuinely clear day. Not a connection failure — verified with a broader `in:inbox` query that the mailbox connection is live.
- Marked "Morning Brief" Done on the Progress Tracker board (2/10).
