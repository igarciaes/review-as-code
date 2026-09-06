# Review as Code (RaC)

Review as Code (RaC) is a lightweight, Git-native practice for representing reviews as version-controlled, human-readable artifacts.

RaC supports reviews of code, documentation, specifications, requirements, architecture, configuration, and other repository artifacts. It is designed for both human and AI-agent workflows.

## Core idea

A review record is an independent artifact from the artifact being reviewed:

- **Reviewer owns the review record.**
- **Author/implementer owns the reviewed artifacts.**
- **Discussion happens externally.**
- **The review records findings, decisions, and verification outcomes—not conversation transcripts.**

A review may contain multiple review rounds before it is closed.

## Repository contents

- `SPEC.md` — normative RaC v0.1 specification
- `AGENTS.md` — instructions for agents working in this repository
- `SKILL.md` — portable RaC agent skill
- `examples/` — reference review records
- `schemas/review.schema.json` — optional validation schema

## Quick workflow

1. Author creates or changes artifacts and opens a review context (for example, a PR).
2. Reviewer creates a review record.
3. Reviewer performs a review round and records findings.
4. Participants discuss findings externally.
5. Reviewer records the resulting decisions.
6. Author/implementer changes the reviewed artifacts.
7. Reviewer performs verification.
8. Additional rounds occur as needed.
9. Reviewer closes the review when complete.

See `SPEC.md` for the complete protocol.
