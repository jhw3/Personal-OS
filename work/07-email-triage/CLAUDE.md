# Email Triage

## Type
Automation (scheduled, on-demand)

## Purpose
Scans unread inbox mail and sorts it into three Gmail labels — Needs Reply, FYI, Noise — so the inbox stops being 200 undifferentiated unread messages and starts being "3 things that actually need a response." Categorize and label only: never drafts, never sends, never archives. A `Triaged` marker label makes each run idempotent — only newly-arrived mail gets processed each time, not the whole backlog again.

## Entry Points
- Scheduled: daily at 8:00 AM, after Morning Brief (see scheduler/schedule.md)
- On-demand: `/email-triage`

## Tools Used
- Gmail MCP (`list_labels`, `create_label`, `search_threads`, `label_thread`)

## Notion Integration
New database: **"Email Triage"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per run (a daily digest, not one row per email — that would flood the database).

**Columns:**
- `Triage` (title) — e.g. "Triage — 2026-07-30"
- `Date` (date)
- `Needs Reply` (number)
- `FYI` (number)
- `Noise` (number)
- `Top Item` (rich text) — the single most important "Needs Reply" thread, one line

**Views:** Table, sorted by Date descending.

Each row's page `content` holds the full list of Needs Reply and FYI threads (subject + sender), not just properties. Noise threads are counted but not listed individually — no value in itemizing newsletter spam.

## Vault Structure
- **Tier 1:** `vault/projects/email-triage/status.md` — Notion IDs, last run, Gmail label IDs.
- **Tier 2:** `vault/projects/email-triage/triages/YYYY-MM-DD.md` — one file per run.

## Vault Reads
- `soul.md` for voice
- `vault/people/{name}.md` to recognize known senders (a known contact's email leans toward Needs Reply/FYI over Noise even if borderline)

## Vault Writes
- New `vault/projects/email-triage/triages/YYYY-MM-DD.md` each run
- Creates `vault/people/{name}.md` for any new sender worth tracking (a real person emailing directly, not a no-reply/marketing address)
- Updates `vault/projects/email-triage/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Weekly Exec Report (inbox load trend), Personal CRM (new contacts from real senders)
- **Depends on:** Gmail MCP; degrades gracefully (reports "nothing new since last run" rather than failing) once the backlog is worked through and only new mail trickles in

## Post-Run (mandatory)
1. Create `vault/people/{name}.md` for any new real-person sender
2. Add `[[wiki links]]` — triage file links to `[[projects/email-triage/status]]` and any new `[[people/name]]` pages
3. Update Notion: create today's row with full `content`
4. Update `vault/index.md`
5. Update `vault/log.md`
6. On first build only: mark "Email Triage" Done on the Progress Tracker board

## Implementation Notes
- Labels used: `Triage: Needs Reply`, `Triage: FYI`, `Triage: Noise`, plus a marker label `Triaged` applied to every thread once processed. Create these via `create_label` on first run if they don't already exist (check `list_labels` first); IDs cached in `vault/projects/email-triage/status.md`.
- Query for each run: `in:inbox is:unread -label:Triaged`, up to 50 threads per page (Gmail search cap). Categorize using sender + subject + snippet already returned by `search_threads` — no need to open every thread individually.
- Categorization heuristic: automated/no-reply/marketing senders (newsletter domains, `noreply@`, `news@`, `updates@`, bulk-send patterns) → Noise. Real person, direct question or request → Needs Reply. Everything else informational but not actionable → FYI. A sender with an existing `vault/people/{name}.md` page leans toward Needs Reply/FYI even if the content looks automated (context wins over pattern-matching).
- On a large first-run backlog, it's fine to process it in full — the `Triaged` label makes every subsequent run only look at what's new.
- If Gmail MCP is unavailable, skip gracefully and note it rather than failing the run.

## Built (2026-07-30, first run)
- Notion database "Email Triage" created directly under the Personal OS parent page. DB ID and data source ID in `vault/projects/email-triage/status.md`.
- View: "All Triages" (table, sorted by Date descending).
- Created 4 Gmail labels (none existed yet): `Triage: Needs Reply` (Label_1), `Triage: FYI` (Label_2), `Triage: Noise` (Label_3), `Triaged` (Label_4). IDs cached in status.md frontmatter under `gmail_labels`.
- First run fully exercised against the real inbox — not just a graceful-degradation test. Processed the entire 61-thread unread backlog: 0 Needs Reply, 10 FYI (mostly account/security notices, including one genuinely important breached-password alert), 51 Noise (Zapier/Slack/Google Cloud/Heroku/Cursor marketing and trial sequences). All labeled and marked Triaged, so the next run only sees new arrivals.
- Marked "Email Triage" Done on the Progress Tracker board (7/10).
