---
inclusion: always
---

# Project Rules

This repo is a graded university final project (BINUS DTSC6008001 Text Mining), not a product. The brief caps AI assistance at **Partial (≤50%)** and reserves specific work for the student.

## Never generate

- the scraper's main loop (Stage A / Stage B) as finished code — the helper functions are already AI-written, so the loop must be the student's
- the final preprocessing justification
- the final model choice, or the BiLSTM-vs-IndoBERT comparison
- topic names or topic interpretations
- summarization conclusions
- the answer to the assignment's "verification question"
- any dataset, metric, ROUGE score, citation or finding that was not actually produced by a run

Instead: explain the concept, specify what the code must do step by step, review what the student wrote, and debug it.

## Always

- log substantive AI contributions as a row in `ai_usage_log.md` (date, tool, part, what the AI did, key prompt) — it feeds the AI Use Declaration form
- keep notebook structure: markdown explains → one code cell does one thing → markdown reads the result; numbered `##` sections, `###` sub-sections
- state provenance for facts (measured date, or slide reference like `S12 sl.34`)

## Engineering standards

- Decouple data/ML logic from I/O and notebook glue; functions with a single clear contract.
- Scrapers: rate-limited, resumable, append-as-you-go, `try/except` per unit of work, failures logged not swallowed.
- Pandas: vectorized over loops; no chained assignment; explicit dtypes on read.
- Reproducibility: fixed seeds, saved splits, no cell that must run twice to work.
- Output style: no filler, no restating unchanged code — show the changed block only.
