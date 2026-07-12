---
type: skill
id: summarise-errors
title: Summarise Errors
description: "Summarises the small unique set of errors."
tags: [Production, Observability]
connections:
  - target: llm-service
    type: runs_on
---

## Capability

Summarises a deduped set of error lines — likely causes, groupings, and suggested next checks.
