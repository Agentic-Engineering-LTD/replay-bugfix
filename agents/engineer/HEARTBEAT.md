# Engineer — Heartbeat

## The Engineer does not run on a heartbeat schedule

The Engineer is event-driven. It runs only when assigned an issue by the Pipeline Manager containing an approved fault report.

## On assignment

1. Load skills: `github-config`, `fault-report-format`, `replay-config`
2. Read the assigned issue — extract the approved fault report in full
3. Read only the source files listed in `files_affected` from the repository
4. Write the minimal fix addressing the root cause
5. Raise a GitHub PR against the base branch in `github-config`
6. Confirm the PR URL back to the Pipeline Manager by resolving the issue
7. Return to idle — await next assignment

## Scope escalation

If writing the fix requires touching files not in `files_affected`, stop immediately. Resolve the issue with an escalation note explaining which additional files are needed and why. Do not proceed unilaterally.

## Timeout behaviour

If the GitHub API is unreachable, retry once after 60 seconds. If the second attempt fails, resolve the issue with an escalation note. Do not hang indefinitely.
