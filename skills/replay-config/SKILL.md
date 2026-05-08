---
name: replay-config
description: Load when any agent needs Replay authentication, test suite configuration, severity thresholds, or client identity. Load before any Replay MCP connection, dashboard poll, or pipeline triage decision. The primary client-specific configuration skill for the Replay Bug Fix Pipeline.
---

# Replay configuration

## Client identity

| Field | Value |
|---|---|
| Client name | [CLIENT_NAME] |
| Application name | [APP_NAME] |
| Application framework | [e.g. React, Vue, Next.js] |
| Primary technical contact | [CONTACT_NAME] |
| Contact email | [CONTACT_EMAIL] |
| Timezone | [e.g. Europe/London] |

## Replay authentication

| Field | Value |
|---|---|
| MCP server URL (universal) | https://dispatch.replay.io/nut/mcp |
| MCP server URL (per-recording) | https://dispatch.replay.io/nut/recording/{recording_id}/mcp |
| API key secret name | REPLAY_API_KEY |
| Workspace ID | [REPLAY_WORKSPACE_ID] |

The API key is stored as a Paperclip secret named `REPLAY_API_KEY`. Never hardcode the key value in this file.

## Test suite configuration

| Field | Value |
|---|---|
| Test runner | Playwright |
| CI provider | GitHub Actions |
| Workflow file | [e.g. .github/workflows/e2e.yml] |
| Playwright project name | replay-chromium |
| Test suite dashboard project ID | [REPLAY_PROJECT_ID] |

## Trigger configuration

| Field | Value |
|---|---|
| Primary trigger | [dashboard_poll or loop_qa_webhook] |
| Dashboard poll interval | [e.g. 5 minutes] |
| Loop QA webhook endpoint | [URL or PENDING] |
| Loop QA API key secret name | [LOOP_QA_API_KEY or PENDING] |

## Failure filtering

### Severity threshold

| Setting | Value |
|---|---|
| Minimum severity to process | [e.g. any_failure, ci_failure_only] |
| Flaky test handling | [skip_known_flaky or process_all] |
| Flaky escalation threshold | [e.g. 3 consecutive failures triggers processing] |

### Test scope

Tests to include (leave blank to include all):

```
[TEST_PATTERN_1]
[TEST_PATTERN_2]
```

Tests to exclude:

```
[EXCLUDED_TEST_PATTERN_1]
```

### Known flaky tests (skip unless threshold exceeded)

```
[KNOWN_FLAKY_TEST_1]
[KNOWN_FLAKY_TEST_2]
```

## Pipeline behaviour

| Setting | Value |
|---|---|
| Analyst timeout | 15 minutes |
| Engineer timeout | 20 minutes |
| Board approval window | 24 hours |
| Max analyst retries on incomplete report | 1 |
| Auto-escalate on approval timeout | yes |

## React application

| Field | Value |
|---|---|
| Is this a React application? | [yes / no] |
| React version | [e.g. 18.x] |
| Run React render analysis by default? | [yes / no] |

If yes, the Analyst will always run the React render analysis sequence (Steps 8) in addition to the standard investigation sequence.

## Code conventions

Note any conventions the Engineer must follow when writing fixes:

```
[e.g. TypeScript strict mode enabled]
[e.g. Single quotes for strings]
[e.g. No semicolons]
[e.g. Tailwind CSS for styling]
```
