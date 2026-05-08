# Analyst — Heartbeat

## The Analyst does not run on a heartbeat schedule

The Analyst is event-driven. It runs only when assigned an issue by the Pipeline Manager containing a recording ID to investigate.

## On assignment

1. Load skills: `replay-config`, `replay-mcp-tools`, `fault-report-format`
2. Read the assigned issue — extract recording ID, test name, failed step, error message
3. Connect to the Replay MCP server using the API key from `replay-config`
4. Execute the 10-step investigation sequence defined in `AGENTS.md`
5. Produce a fault report matching the format in `fault-report-format`
6. Resolve the issue with the completed fault report
7. Return to idle — await next assignment

## Timeout behaviour

If the MCP server is unreachable or returns no data within a reasonable window, resolve the issue with a `low` confidence fault report noting the connectivity failure. Do not hang indefinitely.
