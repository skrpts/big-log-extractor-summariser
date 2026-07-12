---
type: prompt
id: write-error-summary
title: Summarise Errors
description: "Summarises the unique error set."
tags: [Production, Observability]
connections:
  - target: summarise-errors
    type: derived_from
metadata:
  output_format: markdown
  prompt_type: task
---

## Prompt

You are an SRE. Below is the **unique** set of error lines pulled from a large log, plus their count.

### Unique errors

{{steps.dedupe-errors.output}}

### Unique count

{{steps.count-unique.output}}

Summarise: group the errors by likely cause, call out the most frequent or severe, and suggest the first checks to run. Be concise.
