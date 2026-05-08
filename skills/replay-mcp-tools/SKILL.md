---
name: replay-mcp-tools
description: Load when the Analyst needs the full Replay MCP tool reference, investigation sequences, or tool-selection guidance. Also load when the Pipeline Manager needs to understand what the Analyst is capable of. Generic skill — no client-specific configuration required.
---

# Replay MCP tools reference

## Connection

```
Universal server:    https://dispatch.replay.io/nut/mcp
Per-recording:       https://dispatch.replay.io/nut/recording/{recording_id}/mcp
Auth header:         Authorization: {REPLAY_API_KEY}
```

Use the per-recording server when investigating a single known recording — tool calls do not require the recording_id parameter. Use the universal server when the recording_id may change between calls.

---

## Tool catalogue

### Source code and console

| Tool | Purpose | Always run? |
|---|---|---|
| `ListSources` | List all source files. Supports glob filtering. | Only if file paths unknown |
| `ReadSource` | Source code centred on a line, annotated with executed statements | Yes — after SearchSources |
| `SearchSources` | Regex across all files. Results show which matches are in code that ran. | Yes |
| `ConsoleMessages` | All console log, warn and error messages | Yes |
| `Screenshot` | Visual state, mouse position, recent movements at a timestamp | Yes |

### Code inspection

| Tool | Purpose | Always run? |
|---|---|---|
| `DescribePoint` | Variables in scope, code context, full dependency chain at an execution point | Yes |
| `Evaluate` | Evaluate any JS expression at any execution point | Conditional |
| `GetStack` | Full call stack at an execution point | Yes |
| `Logpoint` | Virtual breakpoint — evaluates expression at every hit across the recording. Up to 20 results. | Yes |
| `InspectElement` | Size, layout, DOM ancestry, render info by CSS selector | Conditional — visual/layout bugs |

### Error analysis

| Tool | Purpose | Always run? |
|---|---|---|
| `UncaughtException` | All unhandled JS exceptions with stack traces | Yes |
| `ReactException` | Exceptions that caused React to unmount | React apps only |

### Network and storage

| Tool | Purpose | Always run? |
|---|---|---|
| `NetworkRequest` | All HTTP requests. Filter by URL substring and time range. | Conditional — data failures |
| `LocalStorage` | All localStorage read and write operations | Conditional — stale cache failures |

### React analysis

| Tool | Purpose | Always run? |
|---|---|---|
| `ReactComponentTree` | Component hierarchy at any point | React — if structure unclear |
| `GetPointComponent` | Which React component was rendering at an execution point | React — conditional |
| `DescribeComponent` | Full render history — every render, every prop, every trigger | React — yes |
| `ReactRenders` | Multi-mode render analysis (summary, commits, waste-rank, component, fiber-cause) | React — yes |
| `DescribeReactRender` | Traces a render trigger back to root cause with render counts | React — yes |

### Performance (conditional — use only if failure is performance-related)

| Tool | Purpose |
|---|---|
| `ProfileStatements` | JS statement execution profile between two points |
| `ProfileGraph` | React fiber render and effect dependency graph |
| `ExecutionDelay` | Per-function execution delays in a source file |

### Utility

| Tool | Purpose | Always run? |
|---|---|---|
| `GetPointLink` | Shareable app.replay.io URL for a specific execution point | Yes — final step always |

---

## Standard investigation sequence

```
Step 1  PlaywrightSteps          → identify failed step and timestamp
Step 2  Screenshot               → visual state at failure
Step 3  UncaughtException        → any unhandled exceptions
        ConsoleMessages          → any console errors
        ReactException           → (React only) component unmount errors
Step 4  SearchSources            → find relevant source files
        ReadSource               → read annotated source
Step 5  DescribePoint            → variables, context, dependency chain
        Evaluate                 → (conditional) test a hypothesis
        InspectElement           → (conditional) DOM state for visual bugs
Step 6  Logpoint                 → value changes across all executions
Step 7  GetStack                 → full call stack at failure point
Step 8  ReactRenders (summary)   → (React only) render overview
        DescribeComponent        → (React only) failing component history
        DescribeReactRender      → (React only) render trigger trace
Step 9  NetworkRequest           → (conditional) API failure investigation
        LocalStorage             → (conditional) stale cache investigation
Step 10 GetPointLink             → generate recording link for fault report
```

Stop early only if the root cause is unambiguous before all steps are complete. Always run Step 10 regardless of when the root cause is found.

---

## ReactRenders mode guide

`ReactRenders` supports multiple analysis modes. Use them in this sequence for render-related failures:

1. `summary` — recording-wide overview of render activity and commit counts
2. `waste-rank` — components ranked by wasted renders (renders with no DOM change)
3. `component` — selected component's render history across all commits
4. `commit-fibers` — all fiber instances of the component in a specific commit
5. `fiber-cause` — exact reason a specific fiber re-rendered (prop change, state update, context shift)

For performance investigations: summary → waste-rank → fiber-cause is the direct path.
For render bug investigations: component → commit detail → fiber-cause is the direct path.

---

## Tips for effective investigation

**Source maps must be available.** Without source maps, execution points map to minified bundle locations rather than source files. If source maps are missing, note this in the fault report and set confidence to `low`.

**Keep recordings short.** The Replay team recommends short focused recordings for best MCP analysis quality. Long recordings increase tool response time.

**Logpoint is the most powerful tool.** It sees every execution across the entire recording in one call. A human would need to reproduce and step through manually. Always run it on the suspicious code location before concluding the investigation.

**DescribePoint dependency chain.** Never truncate this output. The full chain is required in the fault report. It shows the complete causal history of how execution arrived at the failure point.

**GetPointLink is mandatory.** Always the final step. The recording link goes into the fault report and the PR description. Without it, the PR is a claim. With it, the developer can verify in one click.
