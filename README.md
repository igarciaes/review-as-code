# Review as Code (RaC)

Review as Code (RaC) is a lightweight, Git-native practice for representing reviews as version-controlled, human-readable artifacts.

RaC supports reviews of code, documentation, specifications, requirements, architecture, configuration, and other repository artifacts. It is designed for both human and AI-agent workflows.

## Core idea

Each finding is an independent, independently writable artifact with two roles and three independent lifecycle dimensions:

- **Two roles: `Author` and `Reviewer`.** The Author creates the artifacts under review and implements accepted findings; the Reviewer creates findings, participates in decisions, and verifies remediation.
- **Three dimensions: Decision, Implementation, and Verification.** They progress independently through append-only logs.
- **State is derived.** A finding's current Decision, Implementation, and Verification values are materialised from its lifecycle logs; the logs remain authoritative.
- **Discussion happens externally; records capture decisions and verification results, not transcripts.**
- **No single-status workflow.** There is no transition matrix; findings close when their dimensions reach a final disposition.
- **Git history is the timeline; no rounds or workflow state are required.**
- **The filesystem is the index; shared mutable files are minimized.**

## Repository contents

- `SPEC.md` — normative RaC v0.5.2 specification
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
4. Author and Reviewer record decisions in each finding's Decision Log.
5. Author implements accepted findings and appends Implementation Log entries.
6. Reviewer verifies remediation and appends Verification Log entries.
7. Findings close when their dimensions reach a final disposition; the review closes when no findings remain outstanding.

See `SPEC.md` for the complete protocol.