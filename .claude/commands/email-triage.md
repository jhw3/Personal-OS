# /email-triage — Sort the Inbox Into Needs Reply / FYI / Noise

Spec: `work/07-email-triage/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/email-triage/status.md`. If `db_id` is missing, this is first run — bootstrap the "Email Triage" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → create view → save IDs to status.md. Also check `list_labels` for `Triage: Needs Reply`, `Triage: FYI`, `Triage: Noise`, `Triaged` — create any missing via `create_label`, save their IDs to status.md's `gmail_labels`.

## Steps
1. Read `vault/projects/email-triage/status.md` for `data_source_id` and Gmail label IDs.
2. Search `in:inbox is:unread -label:Triaged`, up to 50 threads per page.
3. For each thread, categorize using sender + subject + snippet (see heuristic in the spec), cross-referencing `vault/people/{name}.md` for known senders.
4. Apply the matching category label plus `Triaged` to each thread (`label_thread`).
5. Create `vault/people/{name}.md` for any new real-person sender.
6. Write `vault/projects/email-triage/triages/YYYY-MM-DD.md`: counts, the Needs Reply and FYI threads listed (subject + sender), Noise counted only.
7. Create today's row in the Email Triage Notion database with full `content`.
8. On first run only: mark "Email Triage" Done on the Progress Tracker board.
9. Update `vault/projects/email-triage/status.md` (last_run).

## Graceful degradation
No unread mail matching the query → report "nothing new to triage," no row created. Gmail MCP unavailable → skip, note it, don't fail the run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new triages/ entry).
- Append to `vault/log.md`.
- Report to the user: counts per category, the Needs Reply list, Notion link.
