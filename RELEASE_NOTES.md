# Release Notes

## v1.0.1
GH#820 — migrate the 3 builtin steps (extract-errors, dedupe-errors, count-unique) to the node-less `local.builtin` shape (#802 D1b): `id`/`input`/`context`, no backing skill nodes/edges; chained builtins take `input: {{steps.<prev-id>.output}}` (were from_step bindings); downstream refs `{{steps.Count Unique/Dedupe Errors.output}}` → `{{steps.count-unique/dedupe-errors.output}}`. Deterministic output unchanged. contents skills 4→1.

## v1.0.0
Initial release. Reduces a large log deterministically with builtins — `regex-extract` (ERROR lines) → `dedup-merge` (unique) → `count` — then a model summarises only the small unique set. Demonstrates the big-input token cut: the model reads the unique errors, not the whole log.
