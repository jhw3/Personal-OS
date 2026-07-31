# Sprint Tracker

## Type
Automation (scheduled, on-demand)

## Purpose
Reads the "Progress Tracker" Notion database (the board of 10 Personal OS automations seeded during `/setup`), and generates a standup summary: counts of Done / In Progress / To Do, which tasks moved since the last run, and a velocity read (tasks completed per week). This is the spine automation — every other automation built via `/new` marks itself Done on this same board on first build, so Sprint Tracker is what turns that into a readable signal instead of a Notion tab nobody checks.

## Entry Points
- Scheduled: weekdays at 9:00 AM (see scheduler/schedule.md)
- On-demand: `/sprint-tracker`

## Tools Used
- Notion MCP (`notion-query-data-sources`, `notion-fetch`, `notion-create-pages`, `notion-update-page`)

## Notion Integration
No new database. Reads/writes the existing "Progress Tracker" database created during `/setup` bootstrap (IDs in `vault/projects/sprint-tracker/status.md`). Each run creates one new page under the "Personal OS" parent page (not a database row) containing that day's standup writeup.

## Vault Structure
- **Tier 1:** `vault/projects/sprint-tracker/status.md` — Notion IDs, last run, current counts.
- **Tier 2:** `vault/projects/sprint-tracker/standups/YYYY-MM-DD.md` — one file per run, full standup + velocity math.

## Vault Reads
- `vault/projects/sprint-tracker/status.md` for Notion IDs
- `vault/projects/sprint-tracker/standups/` for the previous run (to compute velocity / what moved)
- `soul.md` for voice

## Vault Writes
- New `vault/projects/sprint-tracker/standups/YYYY-MM-DD.md` each run
- Updates `vault/projects/sprint-tracker/status.md` (last_run, current counts)
- Updates `vault/index.md`, `vault/log.md`

## Connections
- **Feeds into:** Weekly Exec Report (pulls velocity + board state for the Friday capstone)
- **Depends on:** the Progress Tracker board itself, which every other automation writes to on first build (each marks itself Done there — see Post-Run step 7 in `/new`)

## Post-Run (mandatory)
1. Create `vault/people/` for new contacts found — N/A for this automation (no people data)
2. Create `vault/business/` for new companies found — N/A
3. Add `[[wiki links]]` between pages — standup file links to `[[projects/sprint-tracker/status]]`
4. Update Notion database entries — mark this automation's own row Done on first build only
5. Update `vault/index.md`
6. Update `vault/log.md`
7. Sprint board already exists (it created the board) — this automation IS the thing that reads it, so no separate "mark Done" needed beyond its own row

## Implementation Notes
- Standup logic: query the Progress Tracker data source for Task/Status/Order, group by Status, count each.
- Velocity: compare current Done count against the Done count recorded in the most recent prior `standups/*.md` file (if any). If this is the first run, velocity is unset ("first standup, no trend yet").
- The Notion standup page is a simple written page (not a database row) with the same content as the vault standup file, placed under the Personal OS parent page.
