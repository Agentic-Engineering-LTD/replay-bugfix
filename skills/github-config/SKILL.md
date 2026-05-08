---
name: github-config
description: Load when the Engineer agent needs repository details, branch strategy, PR template configuration, assignees or labels. Load before any GitHub PR creation or repository read operation.
---

# GitHub configuration

## Repository

| Field | Value |
|---|---|
| Repository owner | [GITHUB_ORG_OR_USER] |
| Repository name | [REPO_NAME] |
| Full repository path | [GITHUB_ORG_OR_USER]/[REPO_NAME] |
| Default base branch | [e.g. main] |
| GitHub API secret name | GITHUB_TOKEN |

The GitHub token is stored as a Paperclip secret named `GITHUB_TOKEN`. It requires `contents: write` and `pull-requests: write` permissions.

## Branch strategy

| Field | Value |
|---|---|
| Fix branch prefix | fix/replay- |
| Branch naming format | fix/replay-{test_name_slug}-{recording_id_short} |
| Example | fix/replay-products-table-edit-a1b2c3 |
| Delete branch after merge | yes |

## PR configuration

### Labels to apply

```
automated-fix
replay-pipeline
[confidence_level]
```

### Assignees (GitHub usernames)

```
[ASSIGNEE_1]
[ASSIGNEE_2]
```

### Reviewers (GitHub usernames or team slugs)

```
[REVIEWER_1]
[REVIEWER_2]
```

### PR size limits

| Setting | Value |
|---|---|
| Maximum files changed | 5 |
| Maximum lines changed | 50 |

If the fix would exceed these limits, the Engineer must escalate to the Pipeline Manager rather than raising the PR. Large fixes require human authorship.

## Notifications

| Setting | Value |
|---|---|
| Post comment on originating PR when fix is raised? | yes |
| Post comment on originating PR when fix is rejected? | yes |
| Slack notification on PR raised? | [yes / no] |
| Slack webhook secret name | [SLACK_WEBHOOK_URL or NONE] |
| Slack channel | [e.g. #engineering or NONE] |

## Source map configuration

| Field | Value |
|---|---|
| Source maps available in recordings? | [yes / no] |
| Use development builds for recordings? | [yes / no] |

Source maps must be available for the Analyst to accurately map execution points back to source files. If source maps are not available, note this here and the Analyst will flag it in the fault report.
