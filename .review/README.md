# Review as Code

Reviews are stored under `reviews/` in this repository.

## Layout

Each review is a directory under `reviews/` containing a `review.md` metadata file and a `findings/` directory with one Markdown file per finding.

- Review IDs follow `R###` (for example `R001`).
- Review IDs are allocated by scanning `reviews/` for existing records and taking the next sequential unused number.
- Finding IDs follow `F###` (for example `F001`).
- Finding IDs are allocated by scanning the review's `findings/` for existing records and taking the next sequential unused number.
- The canonical, globally unique finding ID is `R###-F###`, derived from the review directory and the finding filename.

## Vocabulary

- Decision: `pending`, `accepted`, `alternative`, `rejected`, `accepted_risk`, `deferred`, `not_applicable`.
- Implementation: `pending`, `implemented`.
- Verification: `pending`, `verified`, `failed`, `not_required`.

Finding state is derived from the append-only Decision, Implementation, and Verification logs.

## Severity

critical, high, medium, low, informational.

## Legacy records

R001 and its feedback threads under `feedback/` predate this layout and remain as legacy v0.3.x history. New reviews follow the layout above.

## Active reviews

None.