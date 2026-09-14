---
name: review-as-code
description: Use to perform Review as Code (RaC) operations — review, implement, decide, verify, inspect, and close — for code, documentation, specifications, and other reviewable artifacts. Follow SPEC.md when present; the specification is authoritative over this skill.
license: MIT
metadata:
  author: igarciaes
  version: 0.5.4
---

# Review as Code

Use this skill to perform RaC operations. Follow `SPEC.md` when present; it is authoritative over this skill.

## Discovery

1. Read repository instructions (for example `AGENTS.md`).
2. Locate `.review/README.md` if present and read it for layout, ID formats, and controlled vocabulary.
3. Locate the target review and the target finding.
4. Load only the artifacts required for the requested operation.

Do not load unrelated reviews or findings unless necessary.

Default layout:

```text
.review/
├── README.md
└── reviews/
    └── R042/
        ├── review.md
        └── findings/
            ├── F001.md
            └── F002.md
```

## Roles

- **Reviewer** creates observations and findings, participates in finding decisions, and verifies remediation.
- **Author** creates the artifacts under review, decides the disposition of findings, and implements accepted findings.

There is no separate `Artifact Owner` or `Verifier` role. A policy MAY require independent verification by a different reviewer.

A role must not silently rewrite another role's owned content.

## Findings

- One finding per file.
- Finding IDs are stable and independently addressable.
- Observations must not be silently rewritten.
- Decisions, implementation, and verification are independent dimensions recorded in append-only logs.
- State is derived from the lifecycle logs.

A finding contains:

```text
Finding
├── State
├── Observation
├── Recommendation
├── Decision Log
├── Implementation Log
└── Verification Log
```

## Lifecycle logs

- The **Decision Log** records the disposition of a finding; the Author and the Reviewer may both contribute.
- The **Implementation Log** records implementation work; the Author authors entries and MAY reference commits, pull requests, or changed files.
- The **Verification Log** records verification results; the Reviewer authors entries and records evidence.
- Log entries MUST NOT be rewritten. Append a new entry to record new information.

## Operations

- **Review:** create finding files from inspected artifacts.
- **Implement:** modify the reviewed artifacts; append an Implementation Log entry and update State when authorized to record implementation evidence.
- **Decide:** append a Decision Log entry and update State.
- **Verify:** append a Verification Log entry with evidence and update State.
- **Inspect:** report state without modifying anything.
- **Close:** close findings that reached a final disposition.

Modify the minimum set of files. Do not update unrelated findings.

## Outstanding Findings

A finding is outstanding when it is not closed per `SPEC.md` Section 11. In general, a finding is closed when a final decision exists and any required implementation and verification are complete.

A review has no outstanding findings when all findings are closed.

A review may be closed only when no outstanding findings exist. Before closing, enumerate finding files and check each finding's State; if any finding is outstanding, do not close the review.

## Final Invariant

> The reviewer observes and verifies. The author implements. Both record decisions.

The same agent may perform different roles at different times, but must respect the boundaries of the current operation.