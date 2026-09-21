---
inclusion: always
---

# Obsidian Vault Is The Project Memory

Vault root: `D:\obsi-vault\idea-swirling`
Project notes: `D:\obsi-vault\idea-swirling\text mining AI dev\`
Access: the `obsidian-vault` MCP server (filesystem, scoped to the vault root). Reads are auto-approved; writes require approval.

## Read before acting

At the start of any task in this repo, read the vault notes rather than re-deriving context:

| Note | Holds |
|---|---|
| `text-mining-app.md` | hub: pipeline, repo paths, current state |
| `brief-and-grading.md` | assignment tasks, weights, deliverables, AI-use prohibitions |
| `decisions.md` | settled decisions + rationale, provisional config, open questions |
| `detik-source-notes.md` | verified scraping facts: URL patterns, selectors, quirks, volumes |
| `progress-log.md` | notebook section status, next action, stage specs |
| `lecture-map.md` | lecture sessions mapped to project stages, slide errors |

The repo's `HANDOFF.md` carries the same context in one file; the vault is the version that stays updated.

## Write after acting

When a task changes the project's state, update the vault in the same session:

- section finished, run completed, or blocker hit → `progress-log.md` (status table + "Next action")
- a choice fixed (labels, dates, hyperparameters, methods) → `decisions.md`, with the rationale in the same row
- new verified fact about a data source → `detik-source-notes.md`
- question that needs the lecturer → the open-questions callout in `decisions.md`

Keep the existing shape: YAML frontmatter with `updated:`, `[[wikilinks]]` between notes, `> [!warning]` / `> [!question]` / `> [!bug]` callouts. Bump `updated:` on every edit. Do not restructure notes that already exist; append or edit in place.

## Vault conventions

- One note per concern, linked from `text-mining-app.md`. New notes must be linked from the hub.
- Facts get a provenance marker: measured/verified date, or the slide reference (`S12 sl.34`).
- Never record a metric, count or result that has not actually been produced by a run.
