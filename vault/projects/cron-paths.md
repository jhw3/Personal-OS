---
tags: [projects, cron-setup, scheduling]
created: 2026-08-13
updated: 2026-08-13
---

# Cron/Task Scheduler Binary Paths

Cached so `/cron-setup` doesn't need to re-check every time.

- **OS:** Windows (Task Scheduler, not crontab)
- **claude binary:** `C:\Users\JHW3\AppData\Roaming\npm\claude.ps1`
- **Repo path:** `C:\Users\JHW3\Desktop\personal-os`
- **Wrapper scripts:** `C:\Users\JHW3\.claude\cron-scripts\personal-os\*.ps1` (outside the repo, contains the OAuth token, see [[projects/cron-token|cron-token]])
- **Logs:** `C:\Users\JHW3\Desktop\personal-os\outputs\logs\{name}.log` (gitignored)
