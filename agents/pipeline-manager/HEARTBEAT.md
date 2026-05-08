# Pipeline Manager — Heartbeat

## On every heartbeat

1. Load skills: `replay-config`, `github-config`, `fault-report-format`, `replay-mcp-tools`
2. Poll the Replay Test Suite Dashboard for new failures using the workspace and project IDs in `replay-config`
3. Filter failures against the severity threshold and exclusion list in `replay-config`
4. For each qualifying new failure not already processed this session:
   - Create a Paperclip issue and assign to the Analyst
   - Log the recording ID, test name and timestamp
5. Check for any open Analyst issues awaiting fault report return — escalate if timeout exceeded
6. Check for any open approval gate items awaiting board decision — escalate if 24-hour window exceeded
7. Check for any open Engineer issues awaiting PR confirmation — escalate if timeout exceeded
8. Post a heartbeat summary to the board: failures detected, issues opened, approvals pending, PRs raised

## Heartbeat interval

As configured in `replay-config`. Default: every 5 minutes.

## On first heartbeat after startup

Run a dependency check before processing any failures:
- Confirm all four skills are loaded and contain no `[PLACEHOLDER]` values
- Confirm `REPLAY_API_KEY` secret is present
- Confirm `GITHUB_TOKEN` secret is present
- Confirm Replay Test Suite Dashboard is reachable
- Report status to board before proceeding

## Do nothing if

- No new failures are detected
- All open issues are within their timeout windows
- Dependency check fails — report to board and pause until resolved
