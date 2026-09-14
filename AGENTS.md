# Agent Instructions

This repository defines the Review as Code (RaC) specification and reference artifacts.

## Roles of artifacts

| Path | Role |
|------|------|
| `SPEC.md` | Normative RaC v0.5.2 protocol |
| `SKILL.md` | Portable agent skill; conformant with the Agent Skills spec |
| `AGENTS.md` | Repository instructions (this file) |
| `examples/` | Non-normative reference review records |
| `schemas/review.schema.json` | Optional validation support for review metadata |
| `schemas/finding.schema.json` | Optional validation support for finding files |
| `CHANGELOG.md` | Release history (Keep a Changelog) |
| `LICENSE` | MIT — © 2026 igarciaes and contributors |

## Source of truth

`SPEC.md` is the normative source for RaC v0.5.2. When any other artifact conflicts with it, `SPEC.md` wins. Examples, the skill, and the schemas are derived and must stay consistent.

## Working in this repository

- Prefer human-readable Markdown and platform-neutral language.
- Do not introduce requirements tied to a specific AI platform into the core specification.
- Work on `main`. Keep commits small and focused with concise, imperative messages (e.g. `docs:`, `feat:`, `fix:`).
- Review `git diff` before committing; stage only intended files; never commit secrets.

## Building and validating

This repository has no build step or automated test suite. Validation is manual:

- confirm `SPEC.md` is coherent and uses normative language;
- ensure `SKILL.md` stays aligned with `SPEC.md` and conformant with the Agent Skills spec (https://agentskills.io/specification), including valid `name`, `description`, and optional `license`/`metadata` frontmatter;
- keep examples consistent with normative format or workflow changes;
- validate examples against `schemas/review.schema.json` and `schemas/finding.schema.json` when applicable, using the Markdown-to-schema projection defined in `SPEC.md` §16.1.

## Adopting RaC

When a repository uses Review as Code, read its `.review/README.md` when present for the review layout, ID formats, controlled vocabulary, and active reviews before operating. Findings live as independent files under each review's `findings/` directory.

## Change discipline

When changing a normative concept, update all affected artifacts together:

- `SPEC.md` — the normative protocol;
- `SKILL.md` — if agent behavior changes;
- `examples/` — if normative format or workflow changes;
- `schemas/` — if structured validation is affected;
- `CHANGELOG.md` — add an entry.

Do not change examples or the skill in a way that contradicts `SPEC.md`.

## Versioning and releases

- The version is tracked in `SPEC.md` (`## Version`) and `CHANGELOG.md`.
- Use Semantic Versioning and Keep a Changelog format.
- To release: commit the changes, add a dated `CHANGELOG.md` entry, create an annotated tag (`git tag -a vX.Y.Z -m "..."`), then push the branch and tag.