# Agent Instructions

This repository defines the Review as Code (RaC) specification and reference artifacts.

## Source of truth

`SPEC.md` is the normative source for RaC v0.1.

When modifying the repository:

1. Preserve consistency with `SPEC.md`.
2. Keep `SKILL.md` aligned with the specification.
3. Update examples when normative format or workflow changes.
4. Keep schemas optional unless the specification explicitly changes.
5. Do not introduce requirements tied to a specific AI platform into the core specification.

## Artifact roles

- `SPEC.md`: normative protocol
- `AGENTS.md`: repository instructions
- `SKILL.md`: portable agent behavior
- `examples/`: non-normative examples
- `schemas/`: optional validation support

## Adopting RaC

When a repository uses Review as Code, read its `.review/README.md` when present for the review layout, ID formats, controlled vocabulary, and active reviews before operating.

## Change discipline

When changing a normative concept:

- update `SPEC.md`;
- update the skill if agent behavior changes;
- update affected examples;
- update the schema if structured validation is affected;
- add a `CHANGELOG.md` entry.

Do not change examples or the skill in a way that contradicts `SPEC.md`.

Prefer human-readable Markdown and platform-neutral language.
