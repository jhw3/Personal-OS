# /research-team — Prospect/Account Research

Spec: `work/04-research-team/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/research-team/status.md`. If `db_id` is missing, this is first run — bootstrap the "Research Team" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Type` select options if dropped → create view → save IDs to status.md.

## Usage
- On-demand: `/research-team "Company or Contact Name"` — researches that one subject immediately.
- Weekly (no argument, scheduled Wednesdays 9AM): reads `vault/projects/research-team/queue.md` and researches every entry in it.

## Steps
1. Read `vault/projects/research-team/status.md` for `data_source_id` and `parent_page_id`.
2. Determine subject(s): the command argument if given, otherwise every entry in `vault/projects/research-team/queue.md`. If running in weekly mode and the queue is empty/placeholder-only, skip straight to reporting "nothing queued" — do not run a generic filler search.
3. Check `vault/business/{name}.md` and `vault/people/{name}.md` for existing context on the subject first.
4. WebSearch for background, recent news/signals, and a likely pain point per subject.
5. Write `vault/research/{subject}.md`: background, signals, pain point, suggested angle. Use soul.md voice.
6. Create/update `vault/business/{company}.md` and `vault/people/{name}.md` for anything new surfaced.
7. Create a row per subject in the Research Team Notion database (`notion-create-pages`, parent = data source) with full `content`.
8. On first run only: mark "Research Team" Done on the Progress Tracker board.
9. Update `vault/projects/research-team/status.md` (last_run).

## Graceful degradation
If WebSearch is unavailable, skip the scan, note it in the research file, and don't fail the run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new vault/research/ entries).
- Append to `vault/log.md`.
- Report to the user: subjects researched, key finding per subject, Notion report link(s).
