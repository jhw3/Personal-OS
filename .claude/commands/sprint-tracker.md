# /sprint-tracker — Standup + Velocity from the Progress Tracker Board

Spec: `work/01-sprint-tracker/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/sprint-tracker/status.md`. It should already have `db_id` and `data_source_id` (created during `/setup`). If missing, halt and tell the user to run `/setup` first — this automation does not create the board, it reads one that must already exist.

## Steps
1. Read `vault/projects/sprint-tracker/status.md` for the Progress Tracker `data_source_id` and the Personal OS `parent_page_id`.
2. Query the data source for `Task`, `Status`, `Order`. Group by Status, get counts.
3. Look in `vault/projects/sprint-tracker/standups/` for the most recent prior standup file. If one exists, diff current Done count against it for velocity. If none exists, this is the first standup.
4. Write `vault/projects/sprint-tracker/standups/YYYY-MM-DD.md` with: counts, what moved since last run (if known), velocity read, full task list by status. Use soul.md voice.
5. Create a Notion page under the Personal OS parent page with the same standup content (`notion-create-pages`, parent = `parent_page_id`).
6. On first run only: update this automation's own row ("Sprint Tracker") to Done on the Progress Tracker board.
7. Update `vault/projects/sprint-tracker/status.md` (last_run, current counts).

## Post-run reminders
- Update `vault/index.md` if this is the first run (new standups/ entry).
- Append to `vault/log.md`.
- Report to the user: counts, velocity, Notion standup page link.
