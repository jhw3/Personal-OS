# Content Machine

## Type
Automation (on-demand only, no schedule). Two entry points: `/content-machine` (create) and `/content-plan` (plan the calendar).

## Purpose
Turns real vault signal (research findings, market pulses, wins, business context) into platform-native content across six formats, in soul.md voice, run through a 3-agent pipeline (Researcher → Writer → Editor) so it reads as human-written, not AI-generated. `/content-plan` looks at what's already been made and what's coming up (goals, launches, competitor activity) and proposes a calendar of what to make next. Neither command ever auto-posts or auto-sends — everything is staged for James to review and publish manually.

## Entry Points
- On-demand: `/content-machine "source"` — source is a URL, text, topic, transcript, or a `vault/research/` report. Runs the full pipeline and produces a content kit.
- On-demand: `/content-plan` — reads vault + Notion state, proposes a calendar of topics, gets user approval, seeds approved topics into the Content Library as `Status: Idea`.
- No schedule for either. Always user-initiated.

## Superseded automation
This replaces the original single-command Content Machine (2026-07-30 build): LinkedIn + newsletter only, one "Content Machine" Notion database (`db_id: a79d95b1-0205-4780-8ff3-0e039076083c`). That database and its rows stay as historical record — do not delete or migrate them. All new work goes into the new **"Content Library"** database (see Notion Integration). `vault/projects/content-machine/status.md` tracks both IDs; `content_library_db_id` is the one both new commands read/write.

---

## COMMAND 1: `/content-machine` (Create Content)

### Vault context pull (before asking anything)
Read, in order:
1. `vault/me/goals.md` — what James is trying to achieve; content should serve these goals, not exist for its own sake.
2. `vault/business/brand.md` — company context, tone, what mAxIm never says.
3. `vault/business/` (all files) — products, services, positioning, competitor pages.
4. `vault/research/` (all files) — recent research that could inform this content.
5. `vault/people/` (all files, skip `_`-prefixed placeholders) — audience and competitor context.
6. `vault/projects/content-machine/status.md` and `vault/projects/content-machine/kits/` — what's already been created, so new content doesn't repeat an angle already used.
7. Notion Content Library (`notion-query-data-sources` SQL against `content_library_data_source_id`) — search past `Idea`/`Title` text for the same topic/source to avoid duplicating an angle.

With that context, the agent already knows the audience, the business angle, and the voice for anything the vault has real signal on.

### Follow-up questions (max 2, only what the vault can't answer)
- If no hot take/angle is evident from context: "Any specific hot take or angle you want to push on this?"
- If platforms aren't specified: "Which platforms — all six, or specific ones?"
Skip straight to creation if the user already answered these in their command args, or if the vault context makes the angle obvious (e.g., a research finding with a clear counterintuitive insight).

### 3-agent pipeline
Run these as sequential passes over the material — each pass's output is the input to the next. Do not skip a pass or merge them; the point is friction between research, voice, and QA.

**1. RESEARCHER**
Read the source deeply (don't just summarize it). Extract:
- Key claims and data points
- Quotable lines worth pulling out verbatim
- Frameworks or structures worth naming
- Counterintuitive insights — the thing that makes this worth sharing, not just informing
WebSearch for supporting stats or a fresher angle if the source is thin or dated. Output: a research brief (bullet list of the above, not prose) that the Writer works from.

**2. WRITER**
Take the research brief + soul.md voice. Produce platform-native drafts for each requested format (see Content Formats below). "Platform-native" means each piece follows that platform's actual structure — different hooks, different rhythms, different lengths — not the same paragraph resized. Every claim in a draft must trace back to the research brief or a cited source; never invent a stat.

**3. EDITOR**
Load soul.md fresh for this pass. Hard checks on every piece, reject/rewrite on any hit:
- No em-dashes
- No filler ("it's worth noting," "in today's world," "at the end of the day")
- No parallel triplets ("X, Y, and Z" structures repeated as a rhetorical crutch)
- No uniform sentence length (vary rhythm — soul.md's own examples do this)
- No AI tells: hedging, disclaimers, "as an AI," generic transitions
Score each piece's tone against the actual example lines in soul.md. Rewrite until it would pass as something James wrote himself, not generated.

### Content Formats
Generate only the platforms requested (default: all six if unspecified and the user didn't narrow it in the follow-up).

| Format | Spec |
|---|---|
| **X thread** | 5-8 tweets. Hook tweet is everything — punchy, opinionated. One insight per tweet. No hashtag walls. |
| **LinkedIn post** | 150-300 words. Personal angle. Hook above the fold. Story structure. Hashtags at end only. |
| **Instagram caption** | Hook line. Story body. CTA. Hashtag block after dot-spacer line breaks. |
| **TikTok/Reels script** | Hook in first 2 seconds. 30-60 sec total runtime. Text-overlay notes for muted viewers. Camera/edit notes. |
| **Newsletter** | 300-500 words. Personal opening. One deep take (not a roundup). P.S. kicker line. |
| **Blog/SEO** | Only if the source isn't already a blog post. H1/H2 structure. 1500+ words. Internal links to other vault-derived content/pages where relevant. |

### Metadata (every piece gets all of these)
- **Platform** — X / LinkedIn / Instagram / TikTok / Newsletter / Blog
- **Type** — thread / post / caption / script / newsletter-section / long-form
- **Content Pillar** — Brand / Product / Thought Leadership / Education / Community
- **Idea** — one-line angle that makes this piece unique (the hook, in plain language)
- **Source** — what it was derived from (link, vault path, or reference)
- **Target Audience** — who this is for

### Outputs (every run, all three)
1. **Notion** — **one page per content kit** (per source/topic) in the **Content Library** database (parent: `content_library_data_source_id`, verified per root CLAUDE.md's mandatory post-write check). The page body holds every generated piece in full, organized under `## {Platform}: {Type}` headers, in the order listed in Content Formats. Page properties: `Title` = the kit's topic, `Platform` and `Type` are **multi-select** (every platform/type present in this kit), `Pillar`/`Idea`/`Source`/`Target Audience` describe the kit's overall angle, `Status: Draft`.
2. **Vault** — `vault/projects/content-machine/kits/YYYY-MM-DD-{topic}.md`: all pieces + metadata in one file, source material at the top. Cross-link `[[research/{name}]]` if source was a research report, `[[business/{name}]]` if about a product/company.
3. **Output files** — `outputs/content-machine/YYYY-MM-DD-{topic}/`, one `.md` file per platform format actually generated (e.g. `x-thread.md`, `linkedin.md`, `newsletter.md`).

### Post-run vault learning (mandatory)
- New data/insights the Researcher pass uncovered that aren't already in the vault → add to `vault/research/`.
- New competitors or people mentioned → create/update `vault/business/` or `vault/people/` pages.
- If the user edits or rejects a piece during review, note what they didn't like in `vault/me/writing-style-notes.md` (create if missing) so future passes calibrate — this is how the machine gets better over time, not a one-off note.

---

## COMMAND 2: `/content-plan` (Plan Calendar)

### Vault + Notion context pull (before asking anything)
1. `vault/me/goals.md` — what James is working toward; content should drive these goals.
2. `vault/business/` (all files) — products, launches, positioning.
3. `vault/research/` (all files) — research topics that could become content.
4. `vault/projects/content-machine/` (status.md + kits/) — what's already been created and planned.
5. Notion Content Library (`notion-query-data-sources`) — existing calendar: what's Idea/Draft/Review/Scheduled/Published and when.
6. `vault/business/competitors/` — what competitors are talking about, to fill gaps or counter-position rather than duplicate.

### Questions (ask only what the vault can't answer)
1. "How many weeks?" — always ask, can't be inferred.
2. If `vault/me/goals.md` shows an upcoming launch or milestone: suggest, don't ask blind — "I see {launch} coming up. Want me to plan content around it?"
3. If posting frequency isn't established anywhere in the vault yet: "How often per platform?" (skip if a cadence is already evident from published/scheduled history in the Content Library).

### Steps
1. Cross-reference existing Content Library rows (topics, platforms, pillars, dates already used/planned) against vault research and business context to find gaps: days with no content, platforms underserved, pillars with no recent entries.
2. Suggest topics to fill the gaps. For each: title, platform, type, pillar, idea/angle, source material (vault path or reference), suggested publish date.
3. Present as a table. User approves, edits, or rejects each row — don't bulk-commit without a chance to react.
4. Approved topics get created as rows in the Content Library with `Status: Idea` and the suggested `Publish Date` (parent set correctly, verified per root CLAUDE.md's mandatory post-write check — same rule applies here as any other Notion write).
5. For each approved topic, tell the user: `Run /content-machine with "{topic}" to create the content.`

---

## Notion Integration

New database: **"Content Library"**, created under the Personal OS parent page (ID in `vault/projects/notion-parent-id.md`). **One row per content kit** (one source/topic run of `/content-machine`, or one planned topic from `/content-plan`) — not one row per individual platform piece. A kit's row holds every generated piece in its page body.

**Columns:**
- `Title` (title) — e.g. "Humanoid Robotics Race — Content Kit"
- `Platform` (multi-select: X, LinkedIn, Instagram, TikTok, Newsletter, Blog) — every platform generated for this kit
- `Type` (multi-select: thread, post, caption, script, newsletter-section, long-form) — every type generated for this kit
- `Pillar` (select: Brand, Product, Thought Leadership, Education, Community) — the kit's primary pillar
- `Idea` (rich text) — the overarching hook/angle in plain language
- `Source` (rich text) — what it was derived from
- `Target Audience` (rich text)
- `Status` (select: Idea, Draft, Review, Scheduled, Published)
- `Publish Date` (date)

Full content for every platform piece goes in the page body under `## {Platform}: {Type}` headers, not squeezed into properties. A `/content-plan`-seeded row (Status: Idea) has no body content yet — just the plan metadata — until `/content-machine` is run against it and fills the body in.

**Views (create all four):**
- **Calendar** (calendar view, grouped by `Publish Date`)
- **Pipeline** (board view, grouped by `Status`)
- **By Platform** (board view, grouped by `Platform`)
- **This Week** (table view, filtered to `Publish Date` within the current week, sorted ascending)

## Vault Structure
- **Tier 1:** `vault/projects/content-machine/status.md` — Notion IDs (both legacy Content Machine DB and new Content Library DB), last run.
- **Tier 2:** `vault/projects/content-machine/kits/YYYY-MM-DD-{topic}.md` — one file per `/content-machine` run, full kit. `vault/projects/content-machine/drafts/` (legacy, pre-rebuild) stays as historical record.

## Vault Reads
See per-command sections above. Both commands read `soul.md` for voice and `brand/config/brand-config.md` for brand context (colors/fonts if any visual asset is generated).

## Vault Writes
- `/content-machine`: new `vault/projects/content-machine/kits/YYYY-MM-DD-{topic}.md` each run; updates to `vault/research/`, `vault/business/`, `vault/people/` per the post-run learning rule; `vault/me/writing-style-notes.md` on edits/rejections.
- `/content-plan`: no new Tier-2 file by default (the plan itself lives in Notion as Idea rows) — optionally log the planning session's gap analysis to `vault/projects/content-machine/status.md` if it surfaces a durable pattern worth remembering.
- Both: update `vault/projects/content-machine/status.md` (last_run), `vault/index.md`, `vault/log.md`.

## Connections
- **Feeds into:** nothing downstream — terminal output, content for James to review and post manually.
- **Depends on:** real vault signal (research, business context, goals) to draw from. Degrades gracefully — if `/content-machine` is run with no source and the vault has nothing fresh, say so rather than inventing generic content. If `/content-plan` finds no gaps (calendar already full and balanced), say so rather than forcing suggestions.

## Post-Run (mandatory, both commands)
1. Notion: create/update rows per the Notion Integration schema, `content` field always populated. Verify placement after every create call per root CLAUDE.md's mandatory post-write check (never trust the create call's success response alone).
2. Update `vault/index.md`.
3. Update `vault/log.md`.
4. `/content-machine` only: run the post-run vault learning step.

## Maintenance
The "This Week" view is a hardcoded literal date-range filter (Notion's view DSL doesn't actually support relative-date keywords like `"this_week"`, it silently stores them as a literal exact-match string and returns zero rows, confirmed by testing). Whichever command runs first each Monday should update the view's filter to the new Mon-Sun range via `notion-update-view`, and verify the new range actually returns rows via `notion-query-data-sources` in view mode before trusting it, same as any other Notion write in this system.

## Implementation Notes
- Never fabricate a stat, quote, or result — every claim must trace to the research brief, a cited web source, or something already in the vault.
- The Editor pass is not optional and not a formality — actually reread every piece against the soul.md hard-check list before shipping. If a piece still reads like AI after one rewrite, flag it to the user rather than shipping it anyway.
- If WebSearch is unavailable for the Researcher pass, work from the source material alone and note the limitation rather than failing the run.
- If Notion MCP is unavailable, write the content kit locally (vault + outputs/) and skip the Notion step, noting it — don't fail the whole run.
