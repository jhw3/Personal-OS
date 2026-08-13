# /content-machine — Create Content (3-Agent Pipeline)

Spec: `work/09-content-machine/CLAUDE.md`

## Step 0: Bootstrap check
Read `vault/projects/content-machine/status.md`. If `content_library_db_id` is missing, this is first run for the new architecture — bootstrap the "Content Library" Notion database under the Personal OS parent page (see Notion Protocol in root `CLAUDE.md`): create with the schema below → move under Personal OS parent → ALTER COLUMN for `Platform`/`Type`/`Pillar`/`Status` select options (dropped during creation) → create all 4 views (Calendar, Pipeline, By Platform, This Week) → save IDs to status.md as `content_library_db_id` / `content_library_data_source_id`. Do not touch or migrate the legacy `db_id` (old single-command Content Machine database) already in status.md — it stays as historical record.

**Schema:** Title (title), Platform (multi-select: X/LinkedIn/Instagram/TikTok/Newsletter/Blog), Type (multi-select: thread/post/caption/script/newsletter-section/long-form), Pillar (select: Brand/Product/Thought Leadership/Education/Community), Idea (text), Source (text), Target Audience (text), Status (select: Idea/Draft/Review/Scheduled/Published), Publish Date (date). One row = one content kit (all platforms for one topic), not one row per piece.

## Usage
On-demand only, no schedule: `/content-machine "source"` — source is a URL, text, topic, transcript, or a `vault/research/` report path.

## Steps
1. Pull vault context: `vault/me/goals.md`, `vault/business/brand.md`, all of `vault/business/`, all of `vault/research/`, all of `vault/people/` (skip `_`-prefixed placeholders), `vault/projects/content-machine/status.md` + `kits/`. Query the Content Library data source for past content on the same/similar topic to avoid repeating an angle.
2. Ask at most 2 follow-ups, only for what the vault didn't answer: a specific hot take/angle, and which platforms (skip either question if already answered by context or the command args).
3. **RESEARCHER pass:** deep-read the source, extract key claims/data/quotable lines/frameworks/counterintuitive insights. WebSearch for supporting stats or a fresher angle if useful. Produce a research brief.
4. **WRITER pass:** from the brief + soul.md voice, draft each requested platform format per the specs in the work spec (X thread, LinkedIn, Instagram, TikTok/Reels script, Newsletter, Blog/SEO). Platform-native structure, not resized copies of each other.
5. **EDITOR pass:** reload soul.md, run the hard checks (no em-dashes, no filler, no parallel triplets, no uniform sentence rhythm, no AI tells), rewrite anything that fails, score against soul.md's own example lines.
6. Write `vault/projects/content-machine/kits/YYYY-MM-DD-{topic}.md` with all pieces + full metadata, cross-linked to source vault pages.
7. Write `outputs/content-machine/YYYY-MM-DD-{topic}/` — one `.md` file per platform format generated.
8. Create one Content Library row for the whole kit (`parent: {type: "data_source_id", data_source_id: content_library_data_source_id}`, `Platform`/`Type` as multi-select lists of everything generated, `Pillar`/`Idea`/`Source`/`Target Audience` describing the kit's angle, `Status: Draft`, page body = every piece in full under `## {Platform}: {Type}` headers). If this kit started from an approved `/content-plan` row (`Status: Idea`), update that existing row's content and Status instead of creating a duplicate. Verify placement via `notion-query-data-sources` before reporting success.
9. Post-run vault learning: new research/insight → `vault/research/`; new competitor/person → `vault/business/` or `vault/people/`; if the user edits/rejects a piece, log what they didn't like to `vault/me/writing-style-notes.md`.
10. Update `vault/projects/content-machine/status.md` (last_run).

## Graceful degradation
No fresh vault signal and no source given → say so, don't manufacture generic content. WebSearch unavailable → Researcher pass works from the source alone, note the limitation. Notion MCP unavailable → still write the vault kit + output files, skip the Notion step, note it.

## Post-run reminders
- Update `vault/index.md` if this is the first run (new kits/ entry, or first-ever Content Library bootstrap).
- Append to `vault/log.md`.
- Report to the user: pieces created (platform + one-line idea each), kit file path, output file paths, Content Library links (verified placement).
