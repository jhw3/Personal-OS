# /weekly-exec-report — Rollup of All 9 Automations

Spec: `work/10-weekly-exec-report/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/weekly-exec-report/status.md`. If `db_id` is missing, this is first run — bootstrap the "Weekly Exec Report" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → create view → save IDs to status.md.

## Steps
1. Read `vault/projects/weekly-exec-report/status.md` for `data_source_id`.
2. Read every other automation's `vault/projects/{name}/status.md`: sprint-tracker, morning-brief, market-pulse, research-team, personal-crm, meeting-intel, email-triage, expense-wrangler, content-machine. Query live Notion only if a number is missing or stale.
3. Write `vault/projects/weekly-exec-report/reports/YYYY-MM-DD.md`: one section per automation, honest about quiet weeks — never fabricate activity.
4. Build `outputs/weekly-exec-report/YYYY-MM-DD/exec-report.pdf` with reportlab, brand-styled per `brand/config/brand-config.md`. Delete temp build scripts after.
5. Create this week's row in the Weekly Exec Report Notion database with full `content`.
6. On first run only: mark "Weekly Exec Report" Done on the Progress Tracker board — this completes it.
7. Update `vault/projects/weekly-exec-report/status.md` (last_run).

## Graceful degradation
Any automation with nothing new to report → say so plainly in that section. Any automation's Notion database unreachable → fall back to its vault status.md snapshot, note it may be slightly stale, don't fail the whole report.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new reports/ entry).
- Append to `vault/log.md`.
- Report to the user: the headline from each section, PDF path, Notion link.
