---
name: review-as-code
description: Use to perform Review as Code (RaC) operations — review, implement, verify, inspect, close, and provide feedback — for code, documentation, specifications, and other reviewable artifacts. Follow SPEC.md when present; the specification is authoritative over this skill.
license: MIT
metadata:
  author: igarciaes
  version: 0.3.0
---

# Review as Code Skill

## Purpose

Use this skill to perform Review as Code (RaC) operations for code, documentation, specifications, and other reviewable artifacts.

Follow `SPEC.md` when available. The specification is authoritative over this skill.

## Discover

Before acting:

1. Read repository instructions such as `AGENTS.md`.
2. Locate `.review/README.md` if present and read it for layout, ID formats, controlled vocabulary, and active reviews.
3. Locate existing review records and applicable feedback threads.
4. Identify the applicable review, the current review round, and the requested scope and operation.
5. Determine the assigned role and ownership boundary.
6. Determine whether an existing review is applicable.

Default layout:

```text
.review/
├── README.md
├── reviews/
└── feedback/
```

## Operations

Determine the current operation:

- Review
- Implement
- Verify
- Inspect
- Close
- Feedback

Do not combine operations when doing so violates ownership boundaries.

## Review operation

### Permissions

READ:
- reviewed artifacts
- applicable review records

WRITE:
- review record

DO NOT WRITE:
- reviewed artifacts

### Procedure

1. Identify scope and purpose.
2. Create or select a review.
3. Identify the current round.
4. Inspect artifacts.
5. Record specific findings with stable IDs.
6. Record recommendations and acceptance criteria where useful.
7. Do not implement findings.
8. Do not assume recommendations are accepted.

## Implement operation

### Permissions

READ:
- reviewed artifacts
- applicable review records

WRITE:
- reviewed artifacts

DO NOT WRITE:
- reviewer-owned review records

### Procedure

1. Read findings and decisions.
2. Implement only explicitly accepted decisions or explicit authorized instructions.
3. Respect acceptance criteria.
4. Validate changes.
5. Report ambiguity rather than silently changing decisions.
6. Report progress and completion through your own feedback items (Section 8.5); do not write the review record.

Do not mark findings Verified.

## Verify operation

### Permissions

READ:
- reviewed artifacts
- review records

WRITE:
- review record

DO NOT WRITE:
- reviewed artifacts

### Procedure

1. Read the original finding.
2. Read the decision and acceptance criteria.
3. Inspect current artifacts independently.
4. Run appropriate validation.
5. Record evidence. Evidence SHOULD be reproducible (for example commands, test names, or commit references).
6. Mark Verified only when criteria are satisfied.
7. If work remains, keep the review open and continue with another round as appropriate.

## Feedback operation

### Permissions

READ:
- reviewed artifacts
- applicable review records
- applicable feedback threads

WRITE:
- feedback items authored by the acting agent

DO NOT WRITE:
- feedback items authored by another party
- review records or reviewed artifacts (unless another permitted operation applies)

### Procedure

1. Identify the finding that requires clarification or response.
2. Locate its feedback thread in the repository feedback layout (for example `.review/feedback/R042-F001.md`).
3. Read the thread history and the finding's current status and decision.
4. Append a new feedback item with a new stable ID (for example `R042-F001-FB003`).
5. Record the author role and date.
6. Keep the item substantive and decision-relevant; do not reproduce conversational chatter.
7. Do not change the finding status or decision. Decisions are recorded in the review record by the review owner.
8. Do not edit or renumber existing feedback items.

## Inspect operation

### Permissions

READ:
- reviewed artifacts
- review records
- applicable feedback threads

DO NOT WRITE:
- reviewed artifacts
- review records
- feedback threads

### Procedure

1. Identify the applicable review and current round.
2. Read the review record, applicable feedback threads, and reviewed artifacts.
3. Report the current state without modifying any artifact.

## Close operation

### Permissions

READ:
- reviewed artifacts
- review records

WRITE:
- review record

DO NOT WRITE:
- reviewed artifacts

### Procedure

1. Confirm all applicable findings have terminal outcomes and the review purpose is complete (SPEC Section 13.9).
2. Set the record status to Closed.
3. Record a closure date and a concise rationale for closure.
4. Optionally add an outcome summary section (for example `## Review Outcome`).
5. Do not close a review while actionable findings remain.

## Review rounds

A review MAY contain multiple rounds.

Use another round when:

- previous implementation requires further review;
- unresolved findings remain;
- new findings are discovered during follow-up;
- final verification is not yet complete.

Do not create a new review merely because another iteration is required.

Create a new review for a genuinely new purpose or scope.

## Decisions and disagreement

Discussion belongs outside the review record.

Structured clarification MAY be exchanged through git-managed feedback threads. Append your own items; never edit another party's feedback.

Record concise outcomes, not conversation transcripts.

If an author disagrees:

1. keep the original finding identity;
2. do not let the author rewrite the reviewer record;
3. resolve the disagreement externally;
4. record the resulting decision;
5. implement only if accepted.

If participants cannot reach agreement, the review owner or a designated arbiter records a decision with a rationale that includes a summary of the disagreement. The finding progresses once an authorized decision is recorded.

A revoked decision may return the finding to Discussing or Open. Do not implement a revoked decision.

## Handoff

When one agent completes an operation, the review record SHOULD be updated before another agent operates.

Finding status changes are recorded by the review owner. Implementing agents report progress and completion through their own feedback items (Section 8.5); they do not write the review record. The review owner SHOULD record implementer-reported status changes promptly.

When verifying, read the current finding status and decision before verifying.

When providing feedback, append your items and confirm the thread is current before another agent operates.

State transitions SHOULD be documented by the review owner in the review record.

## Quality rules

Findings should be:

- specific;
- grounded in the artifact;
- understandable without discussion history;
- actionable when action is required.

Avoid vague findings.

## Final invariant

> Reviewer reviews. Implementer implements. Reviewer verifies.

The same agent may perform different roles at different times, but must respect the permissions of the current operation.
