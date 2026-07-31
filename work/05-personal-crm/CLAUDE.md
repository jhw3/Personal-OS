# Personal CRM

## Type
Automation (on-demand, scheduled)

## Purpose
Lightweight sales CRM tracking contacts and pipeline stage against FirmaTRUST's outbound goals ([[me/goals]]: discovery calls booked, reply rate, cadence). Add or update a contact on demand, or let the weekly sync pull in anyone Research Team or Morning Brief already created a `vault/people/` page for, so contacts never get tracked twice. Also flags stale follow-ups — anyone whose next-touch date has already passed.

## Entry Points
- On-demand: `/personal-crm "Name"` — add or update one contact
- Scheduled: weekly, Thursday at 9:00 AM — syncs new `vault/people/` pages into Notion and flags overdue follow-ups (see scheduler/schedule.md)

## Tools Used
- Notion MCP (`notion-create-database`, `notion-move-pages`, `notion-update-data-source`, `notion-create-view`, `notion-create-pages`, `notion-update-page`, `notion-query-data-sources`)

## Notion Integration
New database: **"Personal CRM"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per contact (not per touch — this is a contact roster, not an activity log).

**Columns:**
- `Name` (title)
- `Company` (rich text)
- `Role` (rich text)
- `Stage` (select: New, Contacted, Discovery Call, Proposal, Closed Won, Closed Lost)
- `Last Touch` (date)
- `Next Follow-up` (date)
- `Source` (select: Research Team, Morning Brief, Manual, Other) — where this contact first came from
- `Notes` (rich text) — short summary; full detail lives in the page `content` and the matching `vault/people/{name}.md`

**Views:**
- "Pipeline Board" (board, grouped by Stage)
- "Follow-ups" (table, sorted by Next Follow-up ascending — soonest due first)

## Vault Structure
- **Tier 1:** `vault/projects/personal-crm/status.md` — Notion IDs, last run.
- **Tier 2:** none new — this automation reads and writes `vault/people/{name}.md`, which is shared/owned across automations (Research Team, Morning Brief, Personal CRM all touch it).

## Vault Reads
- `soul.md` for voice
- `vault/me/goals.md` for pipeline context
- `vault/people/*.md` — the weekly sync scans every person page for one missing a `crm_id` in frontmatter (meaning it's not yet in Notion)

## Vault Writes
- Creates/updates `vault/people/{name}.md`: adds/updates `crm_id` (the Notion page ID), `stage`, `last_touch`, `next_follow_up` in frontmatter
- Updates `vault/projects/personal-crm/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Weekly Exec Report (pipeline summary: stage counts, overdue follow-ups)
- **Depends on:** `vault/people/` pages created by Research Team, Morning Brief, or Email Triage (once built) — degrades gracefully (reports "nothing new to sync" rather than failing) when there's nothing there yet

## Post-Run (mandatory)
1. Create `vault/people/{name}.md` for any on-demand contact not already tracked
2. Create `vault/business/{company}.md` if the contact's company isn't already tracked
3. Add `[[wiki links]]` — person pages link to `[[business/company]]` and `[[projects/personal-crm/status]]`
4. Update Notion: create/update the contact's row with full `content`
5. Update `vault/index.md`
6. Update `vault/log.md`
7. On first build only: mark "Personal CRM" Done on the Progress Tracker board

## Implementation Notes
- The `crm_id` frontmatter field on `vault/people/{name}.md` is the sync key — a person page with a `crm_id` is already in Notion; one without needs a new row created on the next weekly sync.
- On-demand mode: if the named person already has a `vault/people/{name}.md` page, update it (and its Notion row via `crm_id`) rather than duplicating.
- Weekly sync: for every person page without a `crm_id`, create a Notion row (`Source` defaults to whichever automation's frontmatter/notes indicate origin, else "Manual"). Also scan existing CRM rows for `Next Follow-up` dates in the past and flag them in the run's report — don't silently change their stage.
- Skip any `vault/people/` file starting with `_` when scanning for sync candidates — `_example-contact.md` is a placeholder template left over from initial scaffolding, not a real contact, and should never be synced.

## Built (2026-07-30, first run)
- Notion database "Personal CRM" created directly under the Personal OS parent page with select options intact for both Stage and Source. DB ID and data source ID in `vault/projects/personal-crm/status.md`.
- Views: "Pipeline Board" (grouped by Stage), "Follow-ups" (sorted by Next Follow-up ascending).
- Discovered `vault/people/_example-contact.md` is a leftover scaffolding template (no frontmatter, bracketed placeholder text) — added the underscore-skip rule above so it's never mistaken for a real contact.
- First-run test confirmed the weekly sync's empty-state path: no real contacts in vault/people/, so 0 rows created, reported cleanly. On-demand mode is built but unverified until run against a real contact name.
- Marked "Personal CRM" Done on the Progress Tracker board (5/10).
- If Notion MCP is unavailable, skip gracefully per the standard bootstrap-unavailable rule.
