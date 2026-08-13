# /content-plan — Plan the Content Calendar

Spec: `work/09-content-machine/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/content-machine/status.md`. If `content_library_db_id` is missing, this shares the Content Library database with `/content-machine` — run that command's bootstrap sequence first (same schema, same IDs). Do not create a second database.

## Usage
On-demand only, no schedule: `/content-plan` (optionally with a topic/course to build the plan around, e.g. `/content-plan "plan 2 weeks of content for my new course on Agentic AI Engineering Bootcamp"`).

## Steps
1. Pull context: `vault/me/goals.md`, all of `vault/business/`, all of `vault/research/`, `vault/projects/content-machine/` (status.md + kits/), `vault/business/competitors/`. Query the Content Library data source for the existing calendar (all rows, their Status/Platform/Pillar/Publish Date).
2. Ask "how many weeks?" if not given in the command args. If `vault/me/goals.md` or the command args surface an upcoming launch/milestone/course, suggest planning around it rather than asking blind. Ask posting frequency per platform only if no cadence is evident from existing Scheduled/Published rows.
3. Identify gaps: days with no content planned, platforms underserved relative to the others, pillars with no recent or upcoming entries.
4. Suggest topics to fill the gaps, pulling real source material from `vault/research/`, `vault/business/`, and any topic/course the user named. For each: title, platform(s), type(s), pillar, idea/angle, source, suggested publish date.
5. Present as a table. Wait for the user to approve, edit, or reject each row — do not auto-commit.
6. For every approved row, create a Content Library page (`parent: {type: "data_source_id", data_source_id: content_library_data_source_id}`, `Status: Idea`, `Publish Date` set, Platform/Type as multi-select from the plan, no body content yet). Verify placement via `notion-query-data-sources` before reporting success.
7. For each approved topic, tell the user: `Run /content-machine with "{topic}" to create the content.`
8. Update `vault/projects/content-machine/status.md` (last_run). If the gap analysis surfaces a durable pattern (e.g. "Instagram consistently empty"), note it there.

## Graceful degradation
If the Content Library already has a full, balanced calendar for the requested window, say so — don't force suggestions just to fill a table. If Notion MCP is unavailable, present the plan to the user directly and note that it wasn't saved to Notion.

## Post-run reminders
- Append to `vault/log.md`.
- Report to the user: weeks planned, gaps found, topics proposed vs. approved, Content Library links for approved rows (verified placement).
