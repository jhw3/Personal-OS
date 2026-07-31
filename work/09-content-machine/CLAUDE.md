# Content Machine

## Type
Automation (scheduled, on-demand)

## Purpose
Drafts LinkedIn post options and a newsletter draft by pulling real source material from the vault — Market Pulse findings, Research Team insights, Sprint Tracker progress, real wins — instead of writing from nothing. Every draft in soul.md voice. Also generates one brand-styled quote-card image per LinkedIn post. Never auto-posts or auto-sends — everything is staged for the user to review and publish manually.

## Entry Points
- Scheduled: weekly, Friday at 10:00 AM (see scheduler/schedule.md)
- On-demand: `/content-machine "topic"` — drafts against a specific topic instead of pulling the week's vault signals

## Tools Used
- Notion MCP (create/update/query tools for this automation's database)
- Gmail MCP (`gmail_create_draft` — stages the newsletter as a draft email to self for easy review/copy, never sent)
- Python/Pillow for quote-card images, using `brand/config/brand-config.md` colors/fonts and `brand/images/logo.png`

## Notion Integration
New database: **"Content Machine"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per piece of content (a LinkedIn post option or a newsletter draft).

**Columns:**
- `Title` (title) — e.g. "LinkedIn — Week of 2026-07-28, Option 1"
- `Date` (date)
- `Type` (select: LinkedIn Post, Newsletter)
- `Status` (select: Draft, Used, Discarded) — the user updates this after deciding what to post
- `Source Signal` (rich text) — which vault page/finding this content pulled from

**Views:** Table, sorted by Date descending.

Each row's `content` holds the full draft text, not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/content-machine/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/projects/content-machine/drafts/YYYY-MM-DD.md` — one file per batch (weekly or on-demand), all drafts from that run together.

## Vault Reads
- `soul.md` for voice
- `vault/log.md` and `vault/projects/sprint-tracker/standups/` for real progress/wins to draw on
- `vault/projects/market-pulse/pulses/` and `vault/research/` for market/prospect insights worth sharing
- `brand/config/brand-config.md` and `brand/images/logo.png` for quote-card styling

## Vault Writes
- New `vault/projects/content-machine/drafts/YYYY-MM-DD.md` each run
- Updates `vault/projects/content-machine/status.md` (last_run)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** nothing downstream — this is a terminal output (content for the user to post)
- **Depends on:** having real signal in the vault to draw from (Sprint Tracker, Market Pulse, Research Team); degrades gracefully to "nothing new to draw on this week" rather than inventing a generic post when the vault has no fresh signal since the last run

## Post-Run (mandatory)
1. Update Notion: create a row per draft with full `content`
2. Update `vault/index.md`
3. Update `vault/log.md`
4. On first build only: mark "Content Machine" Done on the Progress Tracker board

## Implementation Notes
- Never fabricate a stat or result for a post — every claim in a draft must trace to something actually in the vault (a real log entry, a real pulse finding, a real track-record number from `vault/me/role.md`/`vault/business/firmatrust.md`). If nothing new happened since the last run, say so rather than manufacturing a post.
- LinkedIn posts: 2-3 options per batch, distinct angles on the same underlying signal, matching soul.md voice (direct, no em-dashes, no corporate filler, ends with a real question not a hard sell).
- Newsletter: one draft per batch, staged via `gmail_create_draft` to the user's own address for easy review — never sent automatically.
- Quote-card images: one per LinkedIn post batch, Pillow-rendered, Steel Blue/Arc Blue brand palette, logo bottom-right per `brand-config.md`. Saved to `outputs/content-machine/YYYY-MM-DD/`, temp build scripts deleted after per Output Hygiene rules.
- If Gmail MCP is unavailable, skip the newsletter draft step and note it rather than failing the whole run.

## Built (2026-07-30, first run)
- Notion database "Content Machine" created directly under the Personal OS parent page with select options intact. DB ID and data source ID in `vault/projects/content-machine/status.md`.
- View: "All Content" (table, sorted by Date descending).
- First run fully exercised, not filler: drafted 3 LinkedIn post options and 1 newsletter, every claim traced to a real vault page — the day's 8-automation build (vault/log.md), the Expense Wrangler $0-confirmed-spend finding, and the Market Pulse SMB-implementation-gap finding.
- Generated one Pillow quote-card image at `outputs/content-machine/2026-07-30/quote-card-1.png` (Steel Blue/Arc Blue brand palette, mAxIm logo bottom-right) — visually inspected before shipping, not just generated blind.
- Newsletter staged via `gmail_create_draft` to James's own address — confirmed as a draft, never sent.
- Marked "Content Machine" Done on the Progress Tracker board (9/10).
