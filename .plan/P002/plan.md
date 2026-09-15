# P002 — Refactor SKILL.md into a role-agnostic RaC operation skill

**Status:** Draft
**Scope:** `SKILL.md`, `SPEC.md`, `README.md`, `AGENTS.md`, `CHANGELOG.md`
**Planner:** planner-agent
**Created:** 2026-09-15
**PaC version:** v0.5.0

## Objective

Refactor `SKILL.md` so it is implemented as a role-agnostic operation skill rather than as an agent/role definition. The skill must support only the operations required by `SPEC.md` (Review, Implement, Decide, Verify, Inspect, Close), must ensure the spec is followed, must not define or assume any specific role, and must be usable by any agent role. This is a non-breaking change that also releases RaC v0.5.6 (version-string sync only; no spec semantics change).

## Scope

### Included

- Rewriting `SKILL.md` as a role-agnostic operation skill.
- Syncing the RaC version string from v0.5.5 to v0.5.6 across `SPEC.md`, `README.md`, `AGENTS.md`, and `SKILL.md`.
- Adding a `v0.5.6` Keep a Changelog entry to `CHANGELOG.md`.

### Excluded

- Git commit, tag, and push (no repository mutation beyond the working tree).
- Changes to `SPEC.md` normative semantics (version string only).
- Changes to `schemas/` (not affected by this change).
- Changes to `examples/` (no version references present).
- Migration of `.plan/` from v0.3.x to v0.5.0, or updates to `.plan/README.md` and the existing `P001.md` record.

## Constraints

- `SKILL.md` must stay consistent with `SPEC.md`; `SPEC.md` is authoritative.
- `SKILL.md` must remain conformant with the Agent Skills spec (valid `name`, `description`, `license`, and `metadata` frontmatter).
- Roles from `SPEC.md` §9 are presented as enforced ownership constraints, not as an identity assigned to the caller.
- The caller's role is determined by context/policy; the skill assigns no role.
- No commit, tag, or push in this plan.
- Plan and Task records follow PaC v0.5.0 layout (diverging from the repo's documented v0.3.x convention).

## Tasks

- P002-T001
- P002-T002
- P002-T003

## Definition of Done

The Plan is ready to be marked `Completed` when:

- [ ] All required Tasks are `Verified`.
- [ ] `SKILL.md` is role-agnostic, supports only the SPEC-defined operations, and is consistent with `SPEC.md`.
- [ ] RaC version references are synced to v0.5.6 with no remaining `v0.5.5` references in the scoped files.
- [ ] `CHANGELOG.md` records the `v0.5.6` changes.
- [ ] No git commit, tag, or push was performed.
