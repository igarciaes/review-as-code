---
name: review-as-code
description: Use to perform Review as Code (RaC) operations — review, implement, decide, verify, inspect, and close — for code, documentation, specifications, and other reviewable artifacts. This role-agnostic skill executes only the SPEC-defined operations and enforces SPEC conformance without assigning the caller a role. Follow SPEC.md when present; the specification is authoritative over this skill.
license: MIT
metadata:
  author: igarciaes
  version: 0.6.0
---

# Review as Code

This skill executes the RaC operations defined in `SPEC.md` and enforces spec conformance. It is role-agnostic: it does not assign a role to the calling agent. The caller's role is determined by the surrounding context or policy. The skill performs the requested operation and enforces the ownership boundaries defined in `SPEC.md` for whichever role the operation is performed under. Follow `SPEC.md` when present; it is authoritative over this skill.

## Scope

This skill supports only the operations required by `SPEC.md`:

- **Review** — create finding files from inspected artifacts.
- **Implement** — modify reviewed artifacts and record implementation evidence.
- **Decide** — record a finding disposition.
- **Verify** — record verification evidence.
- **Inspect** — report state without modifying anything.
- **Close** — close findings that reached a final disposition.

It performs the operation and confirms the record stays SPEC-conformant. It does not define agent behavior beyond the operations and conformance rules below.

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

## Ownership as enforced constraints

`SPEC.md` §9 defines section-level ownership. This skill does not assign these roles to the caller; it enforces them for the role under which the operation is performed.

- The **Reviewer** owns Observation, Recommendation, Severity, and Location. They MUST NOT be silently rewritten. If incorrect, add clarification or superseding information rather than silently replacing them.
- The **Author** authors Implementation Log entries.
- The **Reviewer** authors Verification Log entries.
- The **Decision Log** is shared: both the Author and the Reviewer MAY contribute. The Author decides the disposition; the Reviewer participates but MUST NOT unilaterally make a decision on behalf of the Author. Exception: the Reviewer MAY unilaterally record an `accepted_risk` decision (`SPEC.md` §9.3).

> This skill does not determine which role the caller is acting as. The caller's role comes from context or policy. The skill enforces the boundaries above for whichever role the current operation is performed under.

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

- The **Decision Log** records the disposition of a finding; both the Author and the Reviewer may contribute.
- The **Implementation Log** records implementation work; the Author authors entries and MAY reference commits, pull requests, or changed files.
- The **Verification Log** records verification results; the Reviewer authors entries and records evidence.
- Log entries MUST NOT be rewritten. Append a new entry to record new information.
- Each log entry has a stable identifier, an actor, a date, and content.

## Operations

For each operation, modify the minimum set of files, respect the ownership constraints above, and run the mandatory post-operation verification below before reporting completion.

Each round performs exactly one operation (`SPEC.md` §18.6). Operations MUST NOT be combined in a single round: for example, `Verify` and `Close` are performed in separate rounds, never in one shot. Updating the derived `State` is part of an operation, not a separate operation.

- **Review** — create finding files from inspected artifacts. Only create new finding files; do not modify unrelated findings.
- **Implement** — modify the reviewed artifacts; when authorized to record implementation evidence, append an Implementation Log entry as the Author and update the derived `State`. Do not implement a finding whose Decision Log lacks an eligible decision (`accepted` by the Author or `accepted_risk` by the Reviewer) (`SPEC.md` §8.1, §8.3, §18.2). Do not modify reviewer-owned sections (Observation, Recommendation, Severity, Location).
- **Decide** — append a Decision Log entry as the Author or Reviewer, per the ownership constraints, and update the derived `State`.
- **Verify** — append a Verification Log entry with evidence as the Reviewer and update the derived `State`. Only modify the target finding.
- **Inspect** — report state without modifying anything.
- **Close** — close findings that reached a final disposition; update review status only if appropriate.

When updating `State`, derive it from the finding record and its lifecycle logs. The lifecycle logs remain the authoritative history; do not rewrite historical entries.

## Post-operation verification

After every operation execution, and before reporting completion, verify that the records produced by the operation stay SPEC-conformant. If any check below fails, correct the deviation before finishing. Do not rewrite lifecycle logs that the current operation did not produce; historical entries must remain untouched.

Concrete checks:

- Every new lifecycle log entry records `id`, `value`, `date`, `actor`, and `content` (`SPEC.md` §10).
- Entry actors match the operation's role boundaries (`SPEC.md` §9.3–9.5).
- The derived `State` matches the finding record and the lifecycle logs (`SPEC.md` §8.5, §20.11).
- Implementation was recorded only after an eligible decision: `implemented` follows an `accepted` (Author) or `accepted_risk` (Reviewer) Decision Log entry (`SPEC.md` §8.1, §8.3, §18.2).
- No historical log entry was rewritten.
- Reviewer-owned sections (Observation, Recommendation, Severity, Location) were not silently modified.
- Only the intended files changed.
- Exactly one operation was performed (`SPEC.md` §18.6).
- `Close` runs only when no finding is outstanding (`SPEC.md` §18.5).
- Schema validation runs when applicable (`SPEC.md` §16.1).
- `Inspect` modifies nothing.

## Outstanding Findings

A finding is outstanding when it is not closed per `SPEC.md` Section 11. In general, a finding is closed when a final decision exists and any required implementation and verification are complete.

A review has no outstanding findings when all findings are closed.

A review may be closed only when no outstanding findings exist. Before closing, enumerate finding files and check each finding's State; if any finding is outstanding, do not close the review.

## Final Invariant

> The reviewer observes and verifies. The author implements. Both record decisions.

This skill assigns no role and is usable by any agent role. The same agent may perform different roles at different times, but must respect the boundaries of the current operation.