---
name: fault-report-format
description: Load when the Analyst needs to produce a fault report, when the Pipeline Manager needs to validate one, or when the Engineer needs to consume one. Defines the exact structure, required fields and confidence level rules for fault reports in the Replay Bug Fix Pipeline.
---

# Fault report format

The fault report is the handoff document between the Analyst and the Engineer, and the document presented to the human approval gate. Every field marked REQUIRED must be present and non-empty. Fields marked OPTIONAL may be omitted if not applicable.

---

## Fault report template

```
FAULT REPORT
============
Pipeline:     Replay Bug Fix Pipeline
Generated:    {ISO_TIMESTAMP}
Agent:        Analyst

FAILURE IDENTIFICATION
----------------------
Test name:        {test_name}
Failed step:      {step_number} — {step_description}
Error message:    {error_message}
Branch / PR:      {branch_or_pr}
Recording ID:     {recording_id}
Failure timestamp: {timestamp}

ROOT CAUSE
----------
Summary (plain English):
{one_to_three_sentence_plain_english_description}

Code location:
  File:   {file_path}
  Line:   {line_number}
  Symbol: {function_or_component_name}

Root cause type:
  [ ] null / undefined reference
  [ ] unhandled promise rejection
  [ ] unhandled API error response
  [ ] React render error
  [ ] timing / race condition
  [ ] stale cached value
  [ ] missing DOM element
  [ ] other: {description}

EVIDENCE
--------
Variable state at failure point:
{variable_state_from_DescribePoint}

Logpoint results at {file_path}:{line_number}:
{logpoint_results — up to 20 hits showing value changes}

Call stack at failure:
{call_stack_from_GetStack}

Dependency chain:
{dependency_chain_from_DescribePoint — do not truncate}

Screenshot state:
{description_of_visual_state_at_failure}

Network requests (if relevant):
{network_request_details or NOT_APPLICABLE}

React render analysis (React apps only):
{react_renders_summary or NOT_APPLICABLE}

REPLAY RECORDING
----------------
Recording link:   {GetPointLink_URL}
Execution point:  {execution_point}
Instructions:     Open the link to inspect the exact failure moment in Replay DevTools.

FIX GUIDANCE
------------
Files to change:
  - {file_path_1}
  - {file_path_2 if applicable}

Estimated lines to change: {number}

Suggested approach:
{one_to_three_sentences_describing_the_fix_approach}
Do not prescribe exact code. Describe what needs to change and why.

CONFIDENCE
----------
Level:       {high / medium / low}
Explanation: {one_sentence_explaining_confidence_level}

SCOPE ASSESSMENT
----------------
Blast radius:  {minimal / moderate / broad}
Risk level:    {low / medium / high}
Notes:         {any_caveats_about_side_effects_or_related_code}

PIPELINE MANAGER NOTES
----------------------
{any_notes_for_the_pipeline_manager_about_escalation_conditions}
```

---

## Field definitions

### Required fields

| Field | Description |
|---|---|
| `test_name` | Exact name of the failed Playwright test |
| `failed_step` | Step number and description from PlaywrightSteps |
| `error_message` | The error message from the failed step |
| `recording_id` | The Replay recording ID |
| `root_cause_summary` | Plain English, 1–3 sentences, no jargon |
| `code_location` | File path and line number — must be specific |
| `call_stack` | Full output of GetStack — do not truncate |
| `dependency_chain` | Full output of DescribePoint dependency chain — do not truncate |
| `replay_link` | GetPointLink URL — mandatory, never omitted |
| `execution_point` | Timestamp and source location of the execution point |
| `files_to_change` | List of files the Engineer should touch |
| `confidence_level` | `high`, `medium` or `low` — see rules below |
| `confidence_explanation` | One sentence explaining the confidence level |
| `scope_assessment` | Blast radius and risk level |

### Optional fields

| Field | When to include |
|---|---|
| `network_request_details` | When failure involves an API response |
| `react_renders_summary` | When app is React and render analysis was run |
| `logpoint_results` | Always include if Logpoint was called |
| `variable_state` | Always include output from DescribePoint |
| `screenshot_state` | Always include description of Screenshot result |

---

## Confidence level rules

### High

Use `high` when:
- A single unambiguous root cause was identified
- The bad value or condition is clearly visible in DescribePoint or Logpoint output
- The fix is a targeted change to one location
- No plausible alternative causes exist

### Medium

Use `medium` when:
- Two plausible root causes exist and the fix addresses both
- The root cause is clear but the fix has potential side effects worth human review
- The recording contains the evidence but the dependency chain is complex
- The fix touches more than two files

### Low

Use `low` when:
- The recording does not contain sufficient information to be certain
- The failure appears intermittent within the recording
- Source maps are missing and execution points cannot be mapped to source accurately
- The root cause involves external services or race conditions not fully captured in the recording

A `low` confidence report is still a valid report. The human reviewer decides whether to approve, reject or escalate.

---

## Validation rules for Pipeline Manager

The Pipeline Manager must reject a fault report that:

- Is missing any required field
- Has `code_location` without a specific file path and line number
- Has `replay_link` missing or containing a placeholder
- Has `confidence_level` set to anything other than `high`, `medium` or `low`
- Has `files_to_change` as an empty list
- Has `root_cause_summary` that is fewer than 20 words
- Has `call_stack` that has been truncated (check for `...` or `[truncated]`)
