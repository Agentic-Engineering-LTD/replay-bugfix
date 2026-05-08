# Dependency check

Use this issue template to run a pre-launch dependency check before the first live pipeline run.

**Assign to:** Pipeline Manager  
**Title:** `Dependency check — {CLIENT_NAME} — {DATE}`

---

## Issue body

```
Please run a full dependency check before the first live run.

Confirm the following and report back with pass/fail for each item:

SKILLS
------
1. replay-config — loaded, no [PLACEHOLDER] values remaining
2. github-config — loaded, no [PLACEHOLDER] values remaining
3. fault-report-format — loaded
4. replay-mcp-tools — loaded

SECRETS
-------
5. REPLAY_API_KEY — present and valid (test: fetch workspace ID from Replay API)
6. GITHUB_TOKEN — present and has contents: write + pull-requests: write permissions
7. SLACK_WEBHOOK_URL — present (or confirmed as not required)

CONNECTIVITY
------------
8. Replay Test Suite Dashboard — accessible with configured project ID
9. Replay MCP server — reachable at https://dispatch.replay.io/nut/mcp
10. GitHub repository — accessible with configured token

AGENTS
------
11. Analyst agent — visible and configured with correct instructions path
12. Engineer agent — visible and configured with correct instructions path

STATUS
------
Report: UNBLOCKED (all checks pass) or BLOCKED (list failing checks)
```
