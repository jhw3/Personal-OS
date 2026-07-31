# /personal-crm — Contacts + Pipeline

Spec: `work/05-personal-crm/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/personal-crm/status.md`. If `db_id` is missing, this is first run — bootstrap the "Personal CRM" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Stage`/`Source` select options if dropped → create views ("Pipeline Board" grouped by Stage, "Follow-ups" sorted by Next Follow-up ascending) → save IDs to status.md.

## Usage
- On-demand: `/personal-crm "Name"` — add or update that one contact.
- Weekly (no argument, scheduled Thursdays 9AM): syncs `vault/people/*.md` pages missing a `crm_id` into Notion, and flags any existing row with a past-due `Next Follow-up`.

## Steps (on-demand)
1. Read `vault/projects/personal-crm/status.md` for `data_source_id`.
2. Check for an existing `vault/people/{name}.md`. If it has a `crm_id`, update that Notion row; otherwise create a new row and write the `crm_id` back into the page's frontmatter.
3. Create/update `vault/business/{company}.md` if the contact's company isn't tracked yet.
4. Report the contact's current stage and next follow-up.

## Steps (weekly sync)
1. Scan `vault/people/*.md` for pages without a `crm_id`, skipping any file starting with `_` (e.g. `_example-contact.md` — a placeholder template, not a real person) — create a Notion row for each real page found, write `crm_id` back to frontmatter.
2. Query the Personal CRM data source for rows where `Next Follow-up` is before today — flag these as overdue in the report.
3. Update `vault/projects/personal-crm/status.md` (last_run).

## Graceful degradation
If there are no `vault/people/` pages yet, or none missing a `crm_id`, report "nothing new to sync" — don't fail or fabricate contacts.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new vault/people/ entries).
- Append to `vault/log.md`.
- Report to the user: contacts added/updated, overdue follow-ups flagged, Notion CRM link.
