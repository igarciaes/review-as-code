# Review as Code (RaC)

Review as Code (RaC) is a lightweight, Git-native practice for representing reviews as version-controlled, human-readable artifacts.

RaC supports reviews of code, documentation, specifications, requirements, architecture, configuration, and other repository artifacts. It is designed for both human and AI-agent workflows.

## Core idea

Each finding is an independent, independently writable artifact:

- **Reviewer owns observations.**
- **Artifact owner owns decisions.**
- **Verifier owns verification.**
- **Discussion happens externally; records capture decisions and verification results, not transcripts.**
- **Findings have exactly two statuses: `open` and `closed`.**
- **Git history is the timeline; no rounds or workflow state are required.**
- **The filesystem is the index; shared mutable files are minimized.**

## Repository contents

- `SPEC.md` — normative RaC v0.4.2 specification
- `AGENTS.md` — instructions for agents working in this repository
- `SKILL.md` — portable RaC agent skill
- `examples/` — reference review records
- `schemas/review.schema.json` — optional validation schema for review metadata
- `schemas/finding.schema.json` — optional validation schema for finding files
- `CHANGELOG.md` — release history (Keep a Changelog)
- `LICENSE` — MIT © 2026 igarciaes and contributors

## Quick workflow

1. Author creates or changes artifacts and opens a review context (for example, a PR).
2. Reviewer creates a review and records findings as independent files.
3. Participants discuss findings externally.
4. Artifact owner records decisions.
5. Author/implementer changes the reviewed artifacts.
6. Verifier independently verifies results.
7. Findings close; the review closes when no findings remain open.

See `SPEC.md` for the complete protocol.