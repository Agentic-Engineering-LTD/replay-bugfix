# Onboarding runbook

Step-by-step guide to deploying the Replay Bug Fix Pipeline for a new customer.

---

## Pre-onboarding checklist

Confirm the following before starting:

- [ ] Customer has an active Replay account
- [ ] Customer has Playwright tests running in CI
- [ ] Replay reporter is configured in `playwright.config.ts`
- [ ] Recordings are uploading to Replay on test failure
- [ ] Customer has a GitHub repository with admin access
- [ ] Paperclip instance is running and accessible

---

## Step 1 — Gather customer details (15 minutes)

Collect the following from the customer before touching any configuration:

| Item | Where to find it |
|---|---|
| Replay workspace ID | Replay dashboard URL: `app.replay.io/team/{workspace_id}` |
| Replay project ID | Test suite dashboard URL |
| Replay API key | Replay settings → API keys |
| GitHub repository full name | e.g. `acmecorp/frontend` |
| GitHub base branch | e.g. `main` |
| GitHub personal access token | Create at github.com/settings/tokens — needs `contents: write`, `pull-requests: write` |
| Playwright workflow file path | e.g. `.github/workflows/e2e.yml` |
| Is the app React? | Ask the customer |
| Preferred PR reviewers | GitHub usernames |
| Slack webhook URL (optional) | Slack app settings |

---

## Step 2 — Fill `replay-config` (10 minutes)

Open `skills/replay-config/SKILL.md`. Replace every `[PLACEHOLDER]` with the values from Step 1.

Key fields:
- `[CLIENT_NAME]` — customer company name
- `[REPLAY_WORKSPACE_ID]` — from Replay dashboard
- `[REPLAY_PROJECT_ID]` — from test suite dashboard
- `[APP_NAME]` and `[APPLICATION_FRAMEWORK]` — e.g. "Acme Frontend" and "React 18"
- React section — set `yes` or `no` based on customer's stack
- Code conventions — ask the customer or infer from the repo

---

## Step 3 — Fill `github-config` (10 minutes)

Open `skills/github-config/SKILL.md`. Replace every `[PLACEHOLDER]`:

- Repository owner and name
- Base branch
- Assignees — at least one
- Reviewers — at least one
- Slack settings — fill or set to `NONE`

---

## Step 4 — Add secrets to Paperclip (5 minutes)

In the customer's Paperclip instance, add:

```
REPLAY_API_KEY     = {customer Replay API key}
GITHUB_TOKEN       = {customer GitHub PAT}
SLACK_WEBHOOK_URL  = {Slack webhook or omit}
LOOP_QA_API_KEY    = {when available — leave blank for now}
```

---

## Step 5 — Create Paperclip company and project (10 minutes)

1. Log into the customer's Paperclip instance
2. Create a new company: `{Customer Name} — Replay Bug Fix`
3. Create a new project connected to this GitHub repository from the start
4. Note the company ID and project ID

---

## Step 6 — Import skills (5 minutes)

Import all four skills from GitHub into the Paperclip project:

- `skills/replay-config/SKILL.md` — client-specific, already filled
- `skills/github-config/SKILL.md` — client-specific, already filled
- `skills/fault-report-format/SKILL.md` — generic
- `skills/replay-mcp-tools/SKILL.md` — generic

---

## Step 7 — Hire Pipeline Manager (5 minutes)

Hire the Pipeline Manager agent:

- Instructions: External mode, pointing to `agents/pipeline-manager/AGENTS.md`
- Model: claude-sonnet-4-6
- Trigger: scheduled heartbeat (configure interval from `replay-config`)

The Pipeline Manager will hire the Analyst and Engineer on first run via board approval.

---

## Step 8 — Run dependency check

Create a manual issue assigned to Pipeline Manager:

**Title:** `Dependency check — pre-launch`

**Description:**
```
Please run a full dependency check before the first live run.

Confirm:
1. All four skills are loaded and fully configured (no [PLACEHOLDER] values remaining)
2. REPLAY_API_KEY secret is present and valid — test by fetching the workspace ID
3. GITHUB_TOKEN secret is present and has correct permissions
4. Replay Test Suite Dashboard is accessible with the configured project ID
5. Analyst and Engineer agents are visible and configured

Report back with pass/fail status for each item.
```

---

## Step 9 — Manual test run (15 minutes)

With the customer, trigger a known test failure in their CI. Confirm:

- [ ] Pipeline Manager detects the failure within the polling interval
- [ ] Analyst is assigned and investigates
- [ ] Fault report is produced and presented to the board
- [ ] Human approval gate appears correctly
- [ ] On approval, Engineer raises a PR
- [ ] PR contains: root cause, Replay link, fix diff, approval attribution

---

## Step 10 — Enable scheduled heartbeat

Once the manual test run passes, enable the Pipeline Manager heartbeat at the interval configured in `replay-config`.

---

## Post-onboarding

- Share the Paperclip instance URL with the customer's technical contact
- Send the bootstrap-ceo invite to the customer's technical contact
- Schedule a 2-week check-in to review the first live pipeline runs
- Update `replay-config` historical baseline after 4 weeks of live data

---

## Estimated onboarding time

| Phase | Time |
|---|---|
| Gather customer details | 15 min |
| Configure skills | 20 min |
| Paperclip setup | 20 min |
| Dependency check and test run | 15 min |
| **Total** | **~70 minutes** |
