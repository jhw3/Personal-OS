# /meeting-intel — Pre-Meeting Briefs + Post-Meeting Notes

Spec: `work/06-meeting-intel/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/meeting-intel/status.md`. If `db_id` is missing, this is first run — bootstrap the "Meeting Intel" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Type` select options if dropped → create view → save IDs to status.md.

## Usage
- Scheduled (no argument, daily 6:30 AM): pre-briefs every meeting on today's calendar.
- On-demand: `/meeting-intel "Meeting name or search term"` — pre-brief if upcoming, post-meeting notes/action-items if it already happened.

## Steps (pre-brief, scheduled or on-demand for an upcoming meeting)
1. Read `vault/projects/meeting-intel/status.md` for `data_source_id`.
2. Pull the meeting(s) from Calendar (today's events for the scheduled run; a search match for on-demand).
3. For each attendee, check the Personal CRM data source and `vault/people/{name}.md`. If nothing on file, say so and suggest `/research-team "name"` — don't fabricate background.
4. Write/append `vault/meetings/{meeting-slug}-YYYY-MM-DD.md` with attendee background, company context, and a suggested angle.
5. Create a Pre-Brief row in the Meeting Intel Notion database with full `content`.

## Steps (post-meeting notes, on-demand for a past meeting)
1. Query Notion meeting notes (`notion-query-meeting-notes`) for the named meeting. If nothing recorded, report that plainly.
2. Extract action items, decisions, and any follow-up commitment.
3. Append to the same `vault/meetings/{meeting-slug}-YYYY-MM-DD.md` file.
4. If a follow-up date surfaced, update the attendee's Personal CRM row (`Next Follow-up`, `Last Touch`).
5. Create a Post-Notes row in the Meeting Intel Notion database with full `content`.

## Steps (either path)
- Update `vault/projects/meeting-intel/status.md` (last_run).
- On first run only: mark "Meeting Intel" Done on the Progress Tracker board.

## Graceful degradation
No meetings today → report that, no rows created. Attendee unknown → note it, suggest `/research-team`, don't fabricate. No recorded notes for a past meeting → report plainly, no action items invented. Calendar or Notion meeting notes MCP unavailable → skip that part, note it, don't fail the run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new vault/meetings/ entries).
- Append to `vault/log.md`.
- Report to the user: meetings briefed/processed, any unknown attendees flagged, Notion link(s).
