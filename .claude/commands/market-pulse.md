# /market-pulse — Weekly Competitive + Market Scan

Spec: `work/03-market-pulse/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/market-pulse/status.md`. If `db_id` is missing, this is first run — bootstrap the "Market Pulse" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Source Type` select options if dropped → create view → save IDs to status.md.

## Steps
1. Read `vault/projects/market-pulse/status.md` for `data_source_id` and `parent_page_id`.
2. Read `vault/business/competitors/watchlist.md`. If it lists real names, WebSearch each one (`"{name}" news OR pricing OR launch`). If empty/placeholder, run 2-3 generic category searches instead (e.g. "AI automation agency SMB 2026").
3. For any named competitor with a new finding, create/update `vault/business/competitors/{name}.md`.
4. Write `vault/projects/market-pulse/pulses/YYYY-MM-DD.md`: signal count, top signal, source type, full findings. Use soul.md voice.
5. Create this week's row in the Market Pulse Notion database (`notion-create-pages`, parent = data source) with full `content`.
6. On first run only: mark "Market Pulse" Done on the Progress Tracker board.
7. Update `vault/projects/market-pulse/status.md` (last_run).

## Graceful degradation
If WebSearch is unavailable, skip the scan, note it in the pulse, and don't fail the run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new pulses/ entry).
- Append to `vault/log.md`.
- Report to the user: signal count, top signal, whether it was a named-competitor scan or generic, Notion pulse page link.
- Remind the user once that `vault/business/competitors/watchlist.md` is empty, if it still is — targeted intel needs real names.
