# /content-machine — LinkedIn Posts + Newsletter from Real Vault Signal

Spec: `work/09-content-machine/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/content-machine/status.md`. If `db_id` is missing, this is first run — bootstrap the "Content Machine" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Type`/`Status` select options if dropped → create view → save IDs to status.md.

## Usage
- Scheduled (no argument, weekly Fridays 10AM): pulls the week's vault signal (log entries, Sprint Tracker standups, Market Pulse pulses, Research Team findings) and drafts from it.
- On-demand: `/content-machine "topic"` — drafts against that specific topic instead.

## Steps
1. Read `vault/projects/content-machine/status.md` for `data_source_id`.
2. Gather source material: recent `vault/log.md` entries, latest Sprint Tracker standup, latest Market Pulse pulse, any Research Team findings since the last content run. Every claim in a draft must trace to one of these — never fabricate a stat.
3. Draft 2-3 LinkedIn post options (soul.md voice) and one newsletter draft.
4. Generate one Pillow quote-card image per LinkedIn post batch (brand colors/logo from `brand/config/brand-config.md`), save to `outputs/content-machine/YYYY-MM-DD/`, delete temp build scripts after.
5. Stage the newsletter as a Gmail draft via `gmail_create_draft` (to self, never sent).
6. Write `vault/projects/content-machine/drafts/YYYY-MM-DD.md` with all drafts + which vault signal each pulled from.
7. Create a Notion row per draft with full `content`.
8. On first run only: mark "Content Machine" Done on the Progress Tracker board.
9. Update `vault/projects/content-machine/status.md` (last_run).

## Graceful degradation
No new vault signal since the last run → report that, don't manufacture a generic post. Gmail MCP unavailable → skip the newsletter draft step only, note it, still deliver the LinkedIn drafts.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new drafts/ entry).
- Append to `vault/log.md`.
- Report to the user: draft options, quote-card image paths, Gmail draft link, Notion link.
