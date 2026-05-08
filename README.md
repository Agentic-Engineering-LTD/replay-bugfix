# Replay Bug Fix Pipeline

**Built by Agentic Engineering · Powered by Replay.io**

An autonomous bug-fixing pipeline that monitors your Playwright test suite, investigates failures using Replay's time-travel debugging, and raises fix PRs — with human approval governing every output.

---

## What it does

1. **Detects** — monitors your Replay Test Suite Dashboard (or Loop QA webhook) for new test failures
2. **Investigates** — connects to the Replay MCP server and runs the full tool suite to identify root cause
3. **Reports** — produces a structured fault report with a Replay recording link pointing to the exact failure moment
4. **Gates** — presents the fault report to a human for review before any code is touched
5. **Fixes** — raises a minimal, targeted GitHub PR with the fix, the root cause explanation, and the Replay link as evidence

The developer reviews a solution, not a mystery.

---

## Architecture

```
Loop QA (beta)              Playwright CI
     ↓                           ↓
     └──────── Failure detected ──┘
                      ↓
            Pipeline Manager agent
                      ↓
              Analyst agent
           (Replay MCP server)
                      ↓
         Human approval gate
                      ↓
             Engineer agent
                      ↓
              GitHub PR raised
```

### Agents

| Agent | Role |
|---|---|
| `pipeline-manager` | Orchestrator — monitors, triages, gates, coordinates |
| `analyst` | Investigator — connects to Replay MCP, produces fault report |
| `engineer` | Fix author — writes minimal fix, raises GitHub PR |

### Skills

| Skill | Type | Edit required |
|---|---|---|
| `replay-config` | Client-specific | Yes — fill client details, API key, test suite IDs |
| `github-config` | Client-specific | Yes — fill repo, branch strategy, assignees |
| `fault-report-format` | Generic | No — upload as-is |
| `replay-mcp-tools` | Generic | No — upload as-is |

---

## Setup

### Prerequisites

- Replay account with API key ([generate here](https://docs.replay.io/reference/ci-workflows/generate-api-key))
- Playwright tests running in CI with Replay reporter configured
- GitHub repository with a personal access token (`contents: write`, `pull-requests: write`)
- Paperclip instance running

### Step 1 — Configure `replay-config`

Edit `skills/replay-config/SKILL.md`. Replace all `[PLACEHOLDER]` values:

- Client name, app name, framework
- `REPLAY_WORKSPACE_ID` — from your Replay dashboard URL
- `REPLAY_PROJECT_ID` — from your test suite dashboard
- Trigger configuration (dashboard poll or Loop QA webhook)
- React configuration (yes/no)
- Code conventions

### Step 2 — Configure `github-config`

Edit `skills/github-config/SKILL.md`. Replace all `[PLACEHOLDER]` values:

- Repository owner and name
- Base branch
- Assignees and reviewers (GitHub usernames)
- Slack configuration (optional)

### Step 3 — Add secrets to Paperclip

Add the following secrets to your Paperclip instance:

| Secret name | Value |
|---|---|
| `REPLAY_API_KEY` | Your Replay workspace API key |
| `GITHUB_TOKEN` | GitHub personal access token |
| `LOOP_QA_API_KEY` | Loop QA API key (when available) |
| `SLACK_WEBHOOK_URL` | Slack webhook URL (if Slack notifications enabled) |

### Step 4 — Import to Paperclip

1. Create a new company in Paperclip for this client
2. Create a new project connected to this GitHub repository
3. Import the four skills from the `skills/` directory
4. Hire the Pipeline Manager agent with instructions from `agents/pipeline-manager/AGENTS.md`
5. The Pipeline Manager will hire the Analyst and Engineer on first run

### Step 5 — Configure Playwright CI

Add Replay reporter to `playwright.config.ts` and `REPLAY_API_KEY` to your CI environment. See the [Replay GitHub Actions guide](https://docs.replay.io/basics/getting-started/record-your-playwright-tests/github-actions).

### Step 6 — Run dependency check

Ask the Pipeline Manager to run a dependency check before the first live run:

```
Please run a dependency check. Confirm all skills are configured,
secrets are in place, and the Replay API key is valid.
Report back with status.
```

---

## Per-customer instance model

Each customer gets their own Paperclip instance stamped from this template. Onboarding a new customer:

1. Clone this repository
2. Fill in `replay-config` and `github-config` with the customer's details
3. Add the customer's secrets to their Paperclip instance
4. Run the dependency check
5. Enable the Pipeline Manager heartbeat

Estimated onboarding time: 1 hour per customer.

---

## Pricing

| Tier | Failures/month | Price |
|---|---|---|
| Starter | Up to 50 | £499/month |
| Growth | Up to 200 | £999/month |
| Scale | Unlimited + SLA | £1,997/month |

---

## Partnership

Built in partnership with [Replay.io](https://replay.io) — the time-travel debugging platform.

Replay records your tests. We fix what breaks.

---

## Support

Built and maintained by Agentic Engineering Ltd  
[agentic-engineering.io](https://agentic-engineering.io)  
jon@agentic-engineering.io
