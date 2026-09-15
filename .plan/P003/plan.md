# P003 — Enforce actor on log entries and one operation per round in RaC

**Status:** Completed
**Scope:** `SPEC.md`, `SKILL.md`, `schemas/finding.schema.json`, `AGENTS.md`, `CHANGELOG.md`, `.plan/P003/`
**Planner:** planner-agent
**Created:** 2026-09-15
**PaC version:** v0.5.0

## Objective

Strengthen RaC conformance and agent behavior in two areas:

1. Ensure every RaC lifecycle log entry records its actor. Tighten `SPEC.md` §20 conformance item 9 so it names the actor requirement, require `content` on `schemas/finding.schema.json` `logEntry` so it matches the mandatory entry fields of `SPEC.md` §10, and add an actor check to the `AGENTS.md` validation checklist.
2. Enforce that each round performs exactly one RaC operation. Add `SPEC.md` §18.6 "One operation per round" and enforce it in `SKILL.md`. For example, `Verify` and `Close` run in separate rounds; they are never performed in one shot.

Record the change in `CHANGELOG.md`.

## Scope

### Included

- `SPEC.md`: new §18.6 normative rule and §20 conformance item 9 tightening.
- `SKILL.md`: one-operation-per-round enforcement in the Operations section.
- `schemas/finding.schema.json`: `logEntry` required fields aligned with `SPEC.md` §10.
- `AGENTS.md`: validation checklist addition for actor on log entries.
- `CHANGELOG.md`: `## [Unreleased]` entry.
- `.plan/P003/`: this Plan and its Task records.

### Excluded

- No RaC version bump (stays v0.5.6).
- No changes to `examples/` (every existing log entry already carries an actor and content; `Pending.` placeholders represent empty logs).
- No changes to `schemas/review.schema.json`.
- No migration of `.plan/` layout or updates to `.plan/README.md` and existing plan records.
- No git commit, tag, or push.

## Constraints

- `SPEC.md` remains authoritative; `SKILL.md` and `schemas/` must stay consistent with it.
- Normative language follows RFC 2119 (MUST, MUST NOT).
- `SKILL.md` must remain conformant with the Agent Skills spec.
- One operation per turn: this Plan is drafted only; it MUST NOT be transitioned to `Planned` or implemented by this operation.
- No commit, tag, or push in this plan.

## Tasks

- P003-T001
- P003-T002
- P003-T003