# P005 — Require an eligible decision before implementing a finding

**Status:** Completed
**Scope:** `SPEC.md`, `SKILL.md`, `examples/R042/findings/F008.md`, `AGENTS.md`, `README.md`, `CHANGELOG.md`, `.plan/P005/`
**Planner:** planner-agent
**Created:** 2026-09-15
**PaC version:** v0.5.0

## Objective

Require an eligible decision before implementation: an Author MUST NOT record implementation
(`implementation=implemented`) unless the finding's Decision Log already records a decision of
`accepted` (Actor: Author) or `accepted_risk` (Actor: Reviewer). This is the only cross-dimension
dependency the specification defines. Add the normative rule to `SPEC.md`, enforce it in `SKILL.md`,
add an example demonstrating the Reviewer-`accepted_risk` → implemented path, and release the change
as RaC v0.6.0 with a Keep a Changelog entry and synced version references.

## Scope

### Included

- `SPEC.md`: §8.1 and §8.3 implementation precondition, §9.3 Reviewer `accepted_risk` exception, §18.2 Implement precondition, §20 conformance item, and version bump `v0.5.6` → `v0.6.0`.
- `SKILL.md`: Implement-operation precondition and a post-operation verification check.
- `examples/R042/findings/F008.md`: new finding `accepted_risk` (Reviewer) → implemented (Author) → verified (Reviewer).
- `AGENTS.md` and `README.md`: version references synced to RaC v0.6.0; `README.md` core-idea adjusted to mention the single implementation precondition.
- `CHANGELOG.md`: `## [v0.6.0] - 2026-09-15` entry.
- `.plan/P005/`: this Plan and its Task records.

### Excluded

- No changes to `schemas/` (`accepted_risk` is already in the `state.decision` enum; preconditions are not projected).
- No changes to existing examples (every `implementation=implemented` record already has an Author `accepted` decision).
- No migration of legacy `.review/` or `.plan/` records.
- No changes to the installed skill copy at `~/.config/opencode/skills/review-as-code/SKILL.md`.
- No git commit, tag, or push.

## Constraints

- `SPEC.md` remains authoritative; `SKILL.md`, `examples/`, `README.md`, and `AGENTS.md` must stay consistent with it.
- Normative language follows RFC 2119 (MUST, MUST NOT).
- The "no general transition matrix" stance is preserved; this precondition is the single explicit cross-dimension dependency.
- One operation per turn: this Plan is drafted only; it MUST NOT be transitioned to `Planned` or implemented by this operation.
- No commit, tag, or push in this plan.

## Tasks

- P005-T001
- P005-T002
- P005-T003
- P005-T004
- P005-T005