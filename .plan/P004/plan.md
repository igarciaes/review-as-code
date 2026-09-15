# P004 — Require post-operation record verification in SKILL.md

**Status:** Completed
**Scope:** `SKILL.md`, `CHANGELOG.md`, `.plan/P004/`
**Planner:** planner-agent
**Created:** 2026-09-15
**PaC version:** v0.5.0

## Objective

Make the review-as-code skill verify the records it produces after every operation execution. Add a mandatory `## Post-operation verification` section to `SKILL.md` that runs after each operation (Review, Implement, Decide, Verify, Inspect, Close), confirming the resulting records stay SPEC-conformant and correcting any deviation before reporting completion.

## Scope

### Included

- `SKILL.md`: new mandatory `## Post-operation verification` section and a reference to it from the `## Operations` intro.
- `CHANGELOG.md`: `## [Unreleased]` entry under `### Changed`.
- `.plan/P004/`: this Plan and its Task records.

### Excluded

- No `SPEC.md` changes (post-operation verification is a skill-behavior requirement, not a normative protocol change).
- No `schemas/` changes (existing schemas already cover the projected log-entry fields the verification step checks).
- No `examples/` changes.
- No RaC version bump (stays v0.5.6).
- No git commit, tag, or push.

## Constraints

- `SKILL.md` MUST remain aligned with `SPEC.md` and conformant with the Agent Skills spec.
- The verification step MUST NOT rewrite lifecycle logs; it checks and corrects only the records produced by the current operation.
- One operation per turn: this Plan is drafted only; it MUST NOT be transitioned to `Planned` or implemented by this operation.
- No commit, tag, or push in this plan.

## Tasks

- P004-T001
- P004-T002