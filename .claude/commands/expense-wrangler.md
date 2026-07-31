# /expense-wrangler — SaaS Subscription Tracking

Spec: `work/08-expense-wrangler/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/expense-wrangler/status.md`. If `db_id` is missing, this is first run — bootstrap the "Expense Wrangler" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create → move (if not auto-placed) → ALTER COLUMN for `Status` select options if dropped → create view → save IDs to status.md.

## Steps
1. Read `vault/projects/expense-wrangler/status.md` for `data_source_id`.
2. Search Gmail for trial/billing/subscription language across the inbox.
3. For each distinct SaaS product with a real subscription signal (trial start, trial end, upgrade attempt, billing), determine status and dates. Pull full body via `get_thread` only when the snippet doesn't confirm a detail needed (date, cost). Never fabricate a cost — leave `Monthly Cost` at 0 and `Cost Confirmed` unchecked if no dollar figure was actually read from an email.
4. Write `vault/projects/expense-wrangler/scans/YYYY-MM.md`: each subscription, status, evidence.
5. Create/update a row per subscription in the Expense Wrangler Notion database with full `content` (the evidence).
6. Regenerate `outputs/expense-wrangler/YYYY-MM-DD/subscriptions.xlsx` via the `/xlsx` skill — real `=SUM` formulas, brand-styled per `brand/config/brand-config.md`. Clean up any temp build scripts after.
7. On first run only: mark "Expense Wrangler" Done on the Progress Tracker board.
8. Update `vault/projects/expense-wrangler/status.md` (last_run).

## Graceful degradation
No new subscription signal since the last scan → report that, no rows updated. Gmail MCP unavailable → skip, note it, don't fail the run.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new scans/ entry).
- Append to `vault/log.md`.
- Report to the user: subscriptions found, statuses, total confirmed monthly cost, Excel + Notion links.
