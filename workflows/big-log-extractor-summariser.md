---
type: workflow
id: big-log-extractor-summariser
title: Big-Log Extractor & Summariser
description: "Extract matching lines from a log, dedupe, count, then summarise the small unique set."
tags: [Production, Observability]
connections:
  - target: summarise-errors
    type: uses
  - target: llm-service
    type: runs_on
metadata:
  estimated_duration: "1-2 minutes"
  trigger: manual
output_step: "summarise-errors"
execution:
  - id: "extract-errors"
    step_type: "local.builtin"
    input: "{{input.log}}"
    context:
      operation: "regex-extract"
      pattern: "^.*\\bERROR\\b.*$"
    output: { name: "matched_lines", type: "text" }
  - id: "dedupe-errors"
    step_type: "local.builtin"
    input: "{{steps.extract-errors.output}}"
    context:
      operation: "sort-lines"
      unique: "true"
    output: { name: "unique_errors", type: "text" }
  - id: "count-unique"
    step_type: "local.builtin"
    input: "{{steps.dedupe-errors.output}}"
    context:
      operation: "count"
      mode: "lines"
    output: { name: "unique_count", type: "text" }
  - skill: "summarise-errors"
    step_type: "generation"
    prompt: "write-error-summary"
    output: { name: "summary", type: "text" }
---

## Overview

Feeding a 50,000-line log to a model is slow, expensive, and often over the context window. This skrpt does the reduction **deterministically first** — extract the ERROR lines, dedupe them, count the unique ones — with zero-token builtins, then hands the model only the small unique set to summarise. The token cut is the value: the model reads tens of lines, not tens of thousands.

## Pipeline Stages

### Stage 1: Extract (builtin)
**regex-extract** pulls every line containing `ERROR`.

### Stage 2: Dedupe (builtin)
**dedup-merge** collapses identical error lines to their unique set.

### Stage 3: Count (builtin)
**count** (lines) reports how many unique errors remain — exact, no model.

### Stage 4: Summarise
The model summarises the unique errors and their likely causes.

## Inputs

| Name | Required | Description | Example |
|------|----------|-------------|---------|
| `{{input.log}}` | Yes | The raw log text | `2026-07-09 ERROR db timeout…` |

## Example Input

```
Log: "paste a large log here; only the unique ERROR lines reach the model."
```
