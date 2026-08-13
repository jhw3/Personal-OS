---
tags: [projects, cron-setup, scheduling]
created: 2026-08-13
updated: 2026-08-13
---

# Cron Token Reference

A Claude Code OAuth token (`claude setup-token`) was configured on 2026-08-13 to run the 9 scheduled automations unattended via Windows Task Scheduler.

**The raw token is never stored in this vault or anywhere in the git repo** — this repo is public on GitHub. The token lives only inside 9 PowerShell wrapper scripts at `C:\Users\JHW3\.claude\cron-scripts\personal-os\*.ps1`, a directory outside the personal-os repo entirely, so it can never be accidentally committed.

- **Configured:** 2026-08-13
- **Token source:** `claude setup-token` (manual, run by James in a separate terminal)
- **Scripts directory (not tracked by git):** `C:\Users\JHW3\.claude\cron-scripts\personal-os\`
- **Scheduler:** Windows Task Scheduler (this machine is Windows, not cron), tasks named `PersonalOS-{name}`
- **To rotate the token:** re-run `claude setup-token`, then update the `$env:CLAUDE_CODE_OAUTH_TOKEN` line in each of the 9 `.ps1` scripts in the directory above (not in this repo).
- **To check what's live:** `schtasks /query /fo TABLE | findstr PersonalOS` in a Windows terminal.

See [[projects/sprint-tracker/status|Sprint Tracker]] for the Progress Tracker board these all feed into, and `scheduler/schedule.md` for the source-of-truth schedule spec these tasks were generated from.
