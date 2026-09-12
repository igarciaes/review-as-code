# Review as Code (RaC)

Review as Code (RaC) is a lightweight, Git-native practice for representing reviews as version-controlled, human-readable artifacts.

RaC supports reviews of code, documentation, specifications, requirements, architecture, configuration, and other repository artifacts. It is designed for both human and AI-agent workflows.

## Core idea

A review record is an independent artifact from the artifact being reviewed:

- **Reviewer owns the review record.**
- **Author/implementer owns the reviewed artifacts.**
- **Discussion happens externally.**
- **The review records findings, decisions, and verification outcomes—not conversation transcripts.**
- **Feedback threads let reviewers and implementers exchange structured, git-managed clarification on findings.**

A review may contain multiple review rounds before it is closed.

## Repository contents

- `SPEC.md` — normative RaC v0.2.2 specification
- `AGENTS.md` — instructions for agents working in this repository
- `SKILL.md` — portable RaC agent skill
- `examples/` — reference review records
- `schemas/review.schema.json` — optional validation schema for review records
- `schemas/feedback.schema.json` — optional validation schema for feedback threads

## Quick workflow

1. Author creates or changes artifacts and opens a review context (for example, a PR).
2. Reviewer creates a review record.
3. Reviewer performs a review round and records findings.
4. Participants discuss findings externally.
5. Reviewers and implementers may exchange structured clarification through git-managed feedback threads.
6. Reviewer records the resulting decisions.
7. Author/implementer changes the reviewed artifacts.
8. Reviewer performs verification.
9. Additional rounds occur as needed.
10. Reviewer closes the review when complete.

See `SPEC.md` for the complete protocol.
