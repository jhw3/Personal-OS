# /morning-brief — Daily Calendar + Email + Board Rundown

Spec: `work/02-morning-brief/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/morning-brief/status.md`. If `db_id` is missing, this is first run — bootstrap the "Morning Brief" Notion database under the Personal OS parent page (see Notion Protocol in the root `CLAUDE.md`): create → move → ALTER COLUMN for `Day Load` select options (Light/Normal/Heavy) → create view → save IDs to status.md.

## Steps
1. Read `vault/projects/morning-brief/status.md` for `data_source_id` and `parent_page_id`.
2. Pull today's calendar events (Google Calendar MCP, `timeMin`/`timeMax` for today, ISO 8601).
3. Pull priority/unread email (Gmail MCP `search_threads`, query like `is:unread is:important`, cap to top 10).
4. Query the Progress Tracker data source (`vault/projects/sprint-tracker/status.md` for its `data_source_id`) for a live Done/In Progress/To Do count.
5. Cross-reference meeting attendees and email senders against `vault/people/` — create a page for anyone new.
6. Write `vault/projects/morning-brief/briefs/YYYY-MM-DD.md`: meetings list, priority email list, board snapshot, any new people flagged. Use soul.md voice.
7. Create today's row in the Morning Brief Notion database (`notion-create-pages`, parent = data source) with full `content` matching the vault brief.
8. On first run only: mark "Morning Brief" Done on the Progress Tracker board.
9. Update `vault/projects/morning-brief/status.md` (last_run).

## Graceful degradation
If Calendar or Gmail MCP errors out or isn't authorized, skip that section, note the skip in the brief, and continue — don't fail the whole run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new briefs/ entry).
- Append to `vault/log.md`.
- Report to the user: meeting count, priority email count, board snapshot, Notion brief page link.
