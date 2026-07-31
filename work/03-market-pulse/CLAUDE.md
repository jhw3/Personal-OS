# Market Pulse

## Type
Automation (scheduled, on-demand)

## Purpose
Weekly scan of the competitive landscape for FirmaTRUST (AI automation for SMBs). Reads a named competitor watchlist from `vault/business/competitors/watchlist.md` and runs web searches against each name for site/pricing/positioning changes and news mentions. If the watchlist is empty (true at first build — no names supplied yet), falls back to a broad market scan on category terms ("AI automation agency for small business", "AI consulting SMB", etc.) so the automation still produces signal instead of erroring out. Surfaces the most important signal of the week, not a wall of search results.

## Entry Points
- Scheduled: weekly, Monday at 9:00 AM (see scheduler/schedule.md)
- On-demand: `/market-pulse`

## Tools Used
- WebSearch (competitor names + category keyword queries)
- Notion MCP (`notion-create-database`, `notion-move-pages`, `notion-update-data-source`, `notion-create-view`, `notion-create-pages`, `notion-query-data-sources`)

## Notion Integration
New database: **"Market Pulse"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). One row per weekly run.

**Columns:**
- `Pulse` (title) — e.g. "Pulse — 2026-08-03"
- `Date` (date)
- `Signals Found` (number) — count of notable findings that week
- `Source Type` (select: Named Competitor, Market News, Mixed, No Watchlist) — reflects whether the watchlist had names yet
- `Top Signal` (rich text) — one-line headline of the most important finding

**Views:** Table, sorted by Date descending.

Each row's page `content` holds the full pulse writeup, not just properties.

## Vault Structure
- **Tier 1:** `vault/projects/market-pulse/status.md` — Notion IDs, last run.
- **Tier 2:** `vault/projects/market-pulse/pulses/YYYY-MM-DD.md` — one file per run, full writeup.
- **Watchlist (read, not owned by this automation):** `vault/business/competitors/watchlist.md` — plain list of competitor names the user maintains. `vault/business/competitors/{name}.md` — one page per competitor once named, updated with each pulse's findings about them.

## Vault Reads
- `soul.md` for voice
- `vault/business/competitors/watchlist.md` for named competitors to search
- `vault/business/competitors/{name}.md` for prior findings on each (to detect what's new vs. already known)

## Vault Writes
- New `vault/projects/market-pulse/pulses/YYYY-MM-DD.md` each run
- Updates `vault/projects/market-pulse/status.md` (last_run)
- Creates/updates `vault/business/competitors/{name}.md` for any competitor with a new signal
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Weekly Exec Report (competitive landscape section)
- **Depends on:** the watchlist being populated for high-signal results — degrades gracefully to generic market scan when empty

## Post-Run (mandatory)
1. Create `vault/people/` — N/A unless a search surfaces a named individual (e.g. a competitor's founder quoted in news) worth tracking
2. Create/update `vault/business/competitors/{name}.md` for every named competitor with a finding this run
3. Add `[[wiki links]]` — pulse file links to `[[projects/market-pulse/status]]` and any `[[business/competitors/name]]` pages touched
4. Update Notion: create this week's row in the Market Pulse database with full `content`
5. Update `vault/index.md`
6. Update `vault/log.md`
7. On first build only: mark "Market Pulse" Done on the Progress Tracker board

## Implementation Notes
- Read the watchlist first. If it lists real competitor names, run one WebSearch per name (e.g. `"{name}" news OR pricing OR launch`) plus a check for site/positioning changes if a URL is on file.
- If the watchlist is empty or only has placeholder text, fall back to 2-3 category searches (e.g. "AI automation agency SMB 2026", "AI consulting small business competitors") and label the row `Source Type: No Watchlist` so it's clear in Notion this was a generic scan, not targeted intel.
- Cap findings to the genuinely notable — this is a signal digest, not a raw search dump.
- If WebSearch is unavailable, skip gracefully and note it in the pulse rather than failing the run.

## Built (2026-07-29, first run)
- Notion database "Market Pulse" created directly under the Personal OS parent page with select options intact. DB ID and data source ID in `vault/projects/market-pulse/status.md`.
- View: "All Pulses" (table, sorted by Date descending).
- `vault/business/competitors/watchlist.md` created empty — user opted to start with a generic scan rather than naming competitors up front. First run correctly fell back to category WebSearches and tagged the row `Source Type: No Watchlist`.
- First pulse surfaced a real finding: SMB AI consulting growing 25.7%/yr with 51% of adopters stuck in an "experimenting, no implementation" phase — flagged as FirmaTRUST's positioning angle.
- Marked "Market Pulse" Done on the Progress Tracker board (3/10).
