# Changelog

## [v0.4.1] - 2026-09-13

### Added

- R002 review record under `.review/reviews/R002/` covering the v0.4.0 simplification changeset (base `4dc92f3`), with findings F001–F004 on the SPEC §7.1 example form, the undocumented severity vocabulary, the schema wording/enum contradiction, and the absent `rac_version` in examples.
- The simplification implementation plan under `.plan/`.

### Changed

- Version references synced to RaC v0.4.1 across `SPEC.md`, `README.md`, `SKILL.md`, `AGENTS.md`, and `CHANGELOG.md`.

## [v0.4.0] - 2026-09-13

### Added

- One-finding-per-file layout: each finding is an independent, independently writable file under a review's `findings/` directory.
- `schemas/finding.schema.json` for optional finding-file validation.
- Section-level ownership: the reviewer owns the observation, the artifact owner owns the decision, and the verifier owns verification.
- Finding lifecycle with only `open | closed` status, separate decision and verification vocabularies, and no transition matrix.
- Outstanding-finding rule: a finding is outstanding when `open`; a review has no outstanding findings when all its findings are `closed`.
- Merge-conflict design rules: creation never modifies existing findings, no global indexes, and shared mutable files minimized.
- Progressive agent loading guidance: load only the artifacts required for the requested operation.
- Lowercase status, decision, verification, and severity vocabularies.

### Changed

- `SPEC.md` rewritten around the simplified model: review metadata is a small `review.md`; the review base reference replaces round tracking; Git history is the timeline.
- `SKILL.md` reduced to a minimal discovery, roles, findings, operations, and outstanding-findings workflow.
- `examples/` restructured into per-review directories (`examples/R042/review.md` with `findings/F###.md`), covering open, accepted-and-verified, rejected, accepted-risk, and no-outstanding-findings scenarios.
- `schemas/review.schema.json` simplified to validate review metadata only.
- `README.md`, `AGENTS.md`, and `.review/README.md` synced to the simplified model; `.review/` documents R001 as a legacy record.

### Removed

- Review rounds as a required protocol concept, including `current_round`.
- The finding state machine and the legal-transition matrix.
- The multi-state finding vocabulary (Discussing, Accepted Alternative, In Progress, Resolved, Verified, Stale, etc.).
- Git-managed feedback threads as a required RaC primitive, `schemas/feedback.schema.json`, and the `examples/feedback-loop.md` reference.
- The requirement to maintain manually synchronized Markdown and JSON representations.

## [v0.3.1] - 2026-09-12

### Changed

- Close review R001: all 12 findings (R001-F001 through R001-F012) independently verified against their acceptance criteria and marked Verified with reproducible evidence (section references, commit hashes, and schema validation).
- Record closure in the R001 review record per §13.9: status `Closed`, closure date, closure rationale, and Review Outcome summary.
- Move R001 from active to closed reviews in `.review/README.md`.
- Sync version references to RaC v0.3.1 across `SPEC.md`, `README.md`, `SKILL.md`, and `AGENTS.md`.

## [v0.3.0] - 2026-09-12

### Changed

- Define the review status vocabulary (`Open | Closed`) in `SPEC.md` §6 and specify closure representation (status `Closed`, closure date, rationale, optional `Review Outcome` section) in §13.9.
- Rewrite the §17.2 handoff rule: the review owner records finding status changes; the implementer reports progress and completion through its own feedback items.
- Add a default finding status-transition table in §10, including Stale reopening semantics and the link between feedback activity and the Discussing state.
- Add SHOULD-level guidance for reproducible verification evidence (§8) and changeset-anchored scope (§4).
- Add review ID allocation guidance (§5).
- Normatize feedback item kinds (Clarification, Response, Report, Confirmation), an optional author identity field, and the Verifier as a feedback participant in §8.5.
- Add the Markdown-to-schema projection mapping in §18.1.
- Reconcile `schemas/review.schema.json` and `schemas/feedback.schema.json` with the revised rules, including `$id` declarations, a consistent `additionalProperties` policy, optional `rac_version`/`closed_date`/`outcome` fields, and open role/decision/kind vocabularies.
- Merge the duplicated discovery sections in `SKILL.md` and add permissions and procedures for the Inspect and Close operations.
- Correct example records to match the status and closure conventions (`R042-F002`, `R017-F001`, `R055`) and fix the broken feedback-thread link in `examples/code-review.md`.
- Add `CHANGELOG.md` and `LICENSE` to the README artifact list.
- Sync version references to RaC v0.3.0 across `SPEC.md`, `README.md`, `SKILL.md`, and `AGENTS.md`.

## [v0.2.3] - 2026-09-12

### Added

- Reviewer decisions for all 12 R001 findings (all Accepted), with amended acceptance criteria for R001-F001, R001-F004, and R001-F009.
- Reviewer feedback responses (`FB002`) in the R001-F001, R001-F004, R001-F005, R001-F006, R001-F009, and R001-F011 threads.
- Version references synced to RaC v0.2.3 across `SPEC.md`, `README.md`, and `SKILL.md`.

## [v0.2.2] - 2026-09-12

### Added

- Implementer feedback threads for R001 findings (12 threads under `.review/feedback/`).
- Version references synced to RaC v0.2.2 across `SPEC.md`, `README.md`, and `SKILL.md`.

## [v0.2.1] - 2026-09-12

### Added

- `.review/` review layout with R001 specification and implementation review (12 findings, all Open).

## [v0.2.0] - 2026-09-12

### Added

- Git-managed feedback loop between reviewers and implementers.
- `SPEC.md` version bumped to RaC v0.2.0.
- New `Review feedback` section (8.5): feedback threads, append-only feedback items with stable IDs (`R###-F###-FB###`), and author ownership of feedback items.
- Feedback layout under `.review/feedback/` in the repository model.
- Feedback permissions for reviewer, implementer, and verifier roles.
- Feedback exchange step in the workflow and feedback loop in the workflow model.
- Agent rule against modifying another party's feedback items.
- Conformance requirement for git-managed feedback records.
- `SKILL.md` Feedback operation with permissions and procedure.
- Optional `schemas/feedback.schema.json` for feedback thread validation.
- `examples/feedback-loop.md` reference thread.

## [v0.1.2] - 2026-09-06

### Changed

- Restructured `AGENTS.md` with artifact role tables, building/validating guidance, git workflow conventions, and versioning/release notes.

## [v0.1.1] - 2026-09-06

### Changed

- `SKILL.md` now includes `name`, `description`, `license`, and `metadata` (author, version) frontmatter to conform to the portable agent skill standard.

## v0.1.0

Initial Review as Code specification and reference implementation.

Includes:

- multiple review records per repository;
- stable review and finding IDs;
- reviewer/implementer ownership boundaries;
- external discussion with persistent decisions;
- independent verification;
- multiple review rounds before closure;
- portable AI agent skill;
- reference examples and optional JSON Schema.
