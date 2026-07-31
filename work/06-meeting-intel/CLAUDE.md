# Meeting Intel

## Type
Automation (scheduled, on-demand)

## Purpose
Closes the loop on meetings. Every morning, ahead of Morning Brief, scans the day's calendar and writes a pre-meeting brief per meeting — attendee background pulled from Personal CRM / `vault/people/`, or flagged for `/research-team` if the attendee is unknown. On demand, processes a specific meeting's notes (from Notion's native meeting notes when available) into action items and decisions, and pushes any new follow-up date into Personal CRM so nothing falls through.

## Entry Points
- Scheduled: daily at 6:30 AM, ahead of Morning Brief (see scheduler/schedule.md)
- On-demand: `/meeting-intel "Meeting name or search term"` — pre-brief if the meeting is upcoming, notes/action-items processing if it already happened

## Tools Used
- Google Calendar MCP (`list_events` / `search_events`)
- Notion MCP (`notion-query-meeting-notes` for post-meeting transcripts/notes if the meeting was recorded in Notion; plus the standard create/update/query tools for this automation's own database)

## Notion Integration
New database: **"Meeting Intel"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per meeting per pass (a meeting can get a Pre-Brief row and later a separate Post-Notes row).

**Columns:**
- `Meeting` (title) — e.g. "Meeting — Acme Discovery Call — 2026-08-03"
- `Date` (date)
- `Attendees` (rich text)
- `Type` (select: Pre-Brief, Post-Notes)
- `Action Items` (number) — count, only meaningful for Post-Notes rows
- `Key Point` (rich text) — the one thing that matters: attendee angle for a pre-brief, top action item for post-notes

**Views:** Table, sorted by Date descending.

Each row's page `content` holds the full brief or notes writeup, not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/meeting-intel/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/meetings/{meeting-slug}-YYYY-MM-DD.md` — one file per meeting; the pre-brief is written first, post-meeting notes get appended to the same file when processed.

## Vault Reads
- `soul.md` for voice
- Calendar for the day's meetings (scheduled run) or the named meeting (on-demand)
- `vault/people/{name}.md` and the Personal CRM data source for attendee background
- `vault/business/{company}.md` for company context on the attendee's employer

## Vault Writes
- New/updated `vault/meetings/{meeting-slug}-YYYY-MM-DD.md` per meeting touched
- Updates `vault/people/{name}.md` with meeting interaction history for attendees
- Updates the attendee's Personal CRM row (`Next Follow-up`, `Last Touch`) when a post-notes pass surfaces a follow-up commitment
- Updates `vault/projects/meeting-intel/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Personal CRM (follow-up dates, last-touch updates), Weekly Exec Report (meeting load + action-item rollup)
- **Depends on:** Calendar MCP for meeting data, Personal CRM / `vault/people/` for attendee context (degrades gracefully — notes "no background on file, consider `/research-team`" rather than failing when an attendee is unknown), Notion meeting notes for post-meeting transcripts (degrades to "no recorded notes — action items not extracted" if none exist for that meeting)

## Post-Run (mandatory)
1. Create `vault/people/{name}.md` for any attendee not already tracked
2. Create `vault/business/{company}.md` if the attendee's company isn't already tracked
3. Add `[[wiki links]]` — meeting file links to attendees, their companies, and `[[projects/meeting-intel/status]]`
4. Update Notion: create a Pre-Brief or Post-Notes row with full `content`
5. Update `vault/index.md`
6. Update `vault/log.md`
7. On first build only: mark "Meeting Intel" Done on the Progress Tracker board

## Implementation Notes
- Daily scheduled run only does pre-briefs (today's meetings, generated before Morning Brief runs so Morning Brief's meeting list can note "briefed" if useful later).
- Post-meeting notes/action-item extraction is on-demand only for now — there's no reliable trigger for "the meeting just ended," so the user runs `/meeting-intel "name"` after a meeting to process it.
- If a meeting has no attendee history anywhere (no Personal CRM row, no `vault/people/` page), don't fabricate background — say so plainly and suggest `/research-team "name"`.
- If Notion meeting notes aren't available for a given meeting (nothing recorded, or the meeting predates Notion note-taking), report that plainly rather than guessing at action items.
- If Calendar or Notion meeting notes MCP is unavailable, skip gracefully and note it rather than failing the run.

## Built (2026-07-30, first run)
- Notion database "Meeting Intel" created directly under the Personal OS parent page with select options intact. DB ID and data source ID in `vault/projects/meeting-intel/status.md`.
- View: "All Meetings" (table, sorted by Date descending).
- First-run test confirmed the empty-day path: no events on the calendar for today, so 0 rows created, reported cleanly. Neither the pre-brief path (needs a real upcoming meeting with attendees) nor the post-notes path (needs a past meeting with recorded Notion notes) has been exercised yet.
- Marked "Meeting Intel" Done on the Progress Tracker board (6/10).
