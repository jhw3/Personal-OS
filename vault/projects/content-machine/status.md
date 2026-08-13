---
db_id: a79d95b1-0205-4780-8ff3-0e039076083c
data_source_id: fb8c0bc8-f7d6-4536-9edf-fc4df09b5850
content_library_db_id: 4c92cb67-9709-4ab4-a9e2-ccc0c0ce01f6
content_library_data_source_id: 26d57de3-94bb-4461-adbe-6c718a17802a
parent_page_id: 3ac25c27-d89b-814b-838f-cfb4f9bfc796
created: 2026-07-30
last_run:
tags: [projects, content-machine]
---

# Content Machine

Two commands: **`/content-machine`** (3-agent pipeline: Researcher, Writer, Editor; turns a source into platform-native content across 6 formats) and **`/content-plan`** (reads the vault + Notion calendar, proposes topics to fill gaps, seeds approved ones as Notion rows). Never auto-posts or auto-sends, everything staged for manual review. Spec in `work/09-content-machine/CLAUDE.md`.

## Content Library (current, both commands write here)
- **Database ID:** 4c92cb67-9709-4ab4-a9e2-ccc0c0ce01f6
- **Data source ID:** 26d57de3-94bb-4461-adbe-6c718a17802a
- **Parent page ID:** 3ac25c27-d89b-814b-838f-cfb4f9bfc796
- **Database URL:** https://app.notion.com/p/4c92cb6797094ab4a9e2ccc0c0ce01f6
- **Schema:** Title, Platform (multi-select), Type (multi-select), Pillar (select), Idea, Source, Target Audience, Status (select), Publish Date. One row = one content kit (all platforms for one topic), body organized under `## {Platform}: {Type}` headers.
- **Views:** Calendar (by Publish Date), Pipeline (board by Status), By Platform (board by Platform), This Week (table, filtered to Publish Date within the current Mon-Sun calendar week, currently 2026-08-10 to 2026-08-16, sorted ascending). **Needs manual refresh every Monday** (`notion-update-view` with a new literal date range): confirmed the DSL's relative-date keywords (e.g. `"this_week"`) don't actually resolve, Notion silently stores them as a literal exact-match string instead and the view returns zero rows, so a hardcoded weekly range is the only option that actually works, not "sorted only" and not a broken relative filter. Verified the literal-range filter itself returns the correct rows (tested via notion-query-data-sources view mode) before relying on it.
- **Created:** 2026-08-13

## Legacy Content Machine (superseded, 2026-07-30 build, kept as historical record, not written to going forward)
- **Database ID:** a79d95b1-0205-4780-8ff3-0e039076083c
- **Data source ID:** fb8c0bc8-f7d6-4536-9edf-fc4df09b5850
- **Database URL:** https://app.notion.com/p/a79d95b1020547808ff30e039076083c
- Latest: [[projects/content-machine/drafts/2026-07-30]]: 3 LinkedIn options + 1 newsletter (Gmail draft, not sent) + 1 quote-card image

## Kits
