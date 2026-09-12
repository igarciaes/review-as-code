# Review as Code

Reviews are stored under `reviews/` in this repository.

## Layout

Each review is a single Markdown file under `reviews/`.

- Review IDs follow `R###` (for example `R001`).
- Review IDs are allocated by scanning `reviews/` for existing records and taking the next sequential unused number.
- Finding IDs follow `R###-F###` (for example `R001-F001`).
- Feedback threads are stored under `feedback/`.
- Feedback thread filenames follow `R###-F###` (for example `R001-F001.md`).
- Feedback item IDs follow `R###-F###-FB###` (for example `R001-F001-FB001`).
- Rounds are represented as sections within the review file.

## Status

This repository uses the full RaC vocabulary from `SPEC.md`.

## Severity

Critical, High, Medium, Low, Informational.

## Active reviews

None.

## Closed reviews

- R001 — Specification and implementation review (closed 2026-09-12)
