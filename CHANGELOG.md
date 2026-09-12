# Changelog

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
