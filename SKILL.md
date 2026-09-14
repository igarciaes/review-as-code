---
name: review-as-code
description: Use to perform Review as Code (RaC) operations — review, implement, decide, verify, inspect, and close — for code, documentation, specifications, and other reviewable artifacts. Follow SPEC.md when present; the specification is authoritative over this skill.
license: MIT
metadata:
  author: igarciaes
  version: 0.4.3
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

- **Reviewer** creates observations and findings.
- **Artifact owner** decides the disposition of findings.
- **Implementer** changes the reviewed artifacts.
- **Verifier** independently verifies results.

A role must not silently rewrite another role's owned content.

## Findings

- One finding per file.
- Finding IDs are stable and independently addressable.
- Observations must not be silently rewritten.
- Decisions are explicit and separate from status.
- Verification records evidence.

## Operations

- **Review:** create finding files from inspected artifacts.
- **Implement:** modify the reviewed artifacts.
- **Decide:** update only the Decision section.
- **Verify:** update only the Verification section.
- **Inspect:** report state without modifying anything.
- **Close:** close findings that reached a final disposition.

Modify the minimum set of files. Do not update unrelated findings.

## Outstanding Findings

A finding is outstanding when Status is `open`.

A review has no outstanding findings when all findings are closed.

A review may be closed only when no outstanding findings exist. Before closing, enumerate finding files and check each Status; if any finding is open, do not close the review.

## Final Invariant

> The reviewer observes. The artifact owner decides. The verifier verifies.

The same agent may perform different roles at different times, but must respect the boundaries of the current operation.
