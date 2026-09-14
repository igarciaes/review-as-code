# Review as Code Specification

## Version

**RaC v0.5.3**

## 1. Purpose

Review as Code (RaC) defines a Git-native, human-readable protocol for recording and managing reviews of repository artifacts.

RaC applies to source code, documentation, specifications, requirements, architecture, configuration, and other reviewable artifacts.

The goals of RaC are to:

- make review findings durable and version controlled;
- support multiple reviews in the same repository;
- store each finding as an independent, independently writable artifact;
- minimize merge conflicts between humans and agents;
- reduce agent context and token costs through progressive loading;
- remain easy for humans to read and edit;
- provide explicit semantics for AI agents;
- separate review, decision, implementation, and verification;
- preserve role ownership over observations, decisions, implementation, and verification;
- derive current finding state from append-only lifecycle logs.

## 2. Normative language

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are normative.

## 3. Core principles

### 3.1 Human-friendly first

Review records MUST be human-readable. Markdown is the canonical representation.

### 3.2 Git native

Review records SHOULD be version controlled alongside the work they review. Git history is the primary timeline; RaC does not duplicate temporal information unless review semantics require it.

### 3.3 Stable identity

Reviews and findings MUST have stable identifiers.

Identifiers MUST NOT change when wording, status, or implementation changes.

### 3.4 Role separation

RaC defines two core roles: `Author` and `Reviewer`.

The reviewer records observations and findings and verifies remediation. The author creates the artifacts under review and implements accepted findings. Both author and reviewer participate in finding decisions.

A reviewer MUST NOT modify artifacts under review while acting as reviewer. An author MUST NOT modify reviewer-owned review records while acting as author. No role may silently rewrite another role's owned content. See Section 9.

### 3.5 Explicit decisions

A recommendation is not permission to modify an artifact.

An actionable change requires an explicit accepted decision or explicit instruction from an authorized human.

### 3.6 Independent verification

Implementation and verification are distinct operations.

A policy MAY require that verification be performed by a different reviewer than the reviewer who created the finding, without introducing a separate `Verifier` role.

### 3.7 Discussion is external

The review record MUST NOT be used as a conversation transcript.

Discussion MAY occur in pull requests, issues, chat, meetings, or other systems. RaC records the resulting decision and verification result, not the conversation.

### 3.8 Minimize shared mutable files

Findings MUST be stored so that most operations create one file or modify one file. Shared mutable files MUST be minimized to reduce merge conflicts. See Section 13.

### 3.9 Append-only lifecycle logs

Lifecycle progression MUST be recorded in append-only logs.

Historical log entries MUST NOT be rewritten as part of normal lifecycle progression. Current state is derived from the logs. See Sections 10 and 11.

## 4. Terminology

### Review

A durable assessment process covering one or more artifacts for a defined purpose and scope.

### Finding

An observation, concern, recommendation, or other review result recorded within a review. A finding is an independently addressable artifact.

### Observation

The reviewer's description of the finding, its location, severity, and recommendation.

### Decision

The agreed disposition for a finding, recorded in the Decision Log.

### Implementation

A change to reviewed artifacts intended to satisfy an accepted decision, recorded in the Implementation Log.

### Verification

An independent assessment that implementation satisfies the decision and applicable acceptance criteria, recorded in the Verification Log with evidence.

### State

The derived, materialised representation of a finding's current Decision, Implementation, and Verification values. See Section 8.5.

### Decision Log

The append-only lifecycle log recording the decisions made about a finding.

### Implementation Log

The append-only lifecycle log recording the implementation work performed for a finding.

### Verification Log

The append-only lifecycle log recording the verification results for a finding.

### Author

The person or agent who creates the artifacts under review and implements accepted findings.

### Reviewer

The person or agent who reviews artifacts, creates findings, participates in finding decisions, and verifies remediation.

### Scope

The set of reviewed artifacts covered by a review, expressed as file paths, directory paths, or other artifact references.

When a review covers a changeset, the scope SHOULD include a commit or pull-request reference so the review remains reproducible.

### Purpose

The reason or objective for conducting a review, expressed as free text.

### Outstanding finding

A finding that has not reached closure. See Section 12.

## 5. Repository model

A repository MAY contain many reviews.

The default layout is:

```text
.review/
├── README.md
└── reviews/
    └── R042/
        ├── review.md
        └── findings/
            ├── F001.md
            ├── F002.md
            └── F003.md
```

Repositories MAY define another layout in `.review/README.md`.

Each finding MUST be stored in its own file. A single mutable document MUST NOT be used to represent multiple findings of the same review.

A single global review file MUST NOT be used to represent multiple unrelated reviews.

Historical records that predate this layout MAY remain in their original layout as legacy artifacts.

### 5.1 Review README

`.review/README.md` describes the repository's review layout and conventions. It is a discovery aid.

It SHOULD document:

- the review layout and the paths of review records and finding files;
- the review ID convention (for example `R###`);
- the finding ID convention (for example `F###`);
- the controlled decision, implementation, and verification vocabularies when a subset is used;
- the severity vocabulary when a subset is used;
- active reviews when useful.

It MUST NOT be required to change when creating a review, creating a finding, closing a finding, or verifying a finding. The filesystem is the primary index.

### 5.2 ID allocation

Before allocating a new review ID, an agent SHOULD scan the existing review directories and take the next sequential unused ID (for example, the next `R###`).

Before allocating a new finding ID, an agent SHOULD scan the target review's `findings/` directory and take the next sequential unused ID (for example, the next `F###`).

The convention MAY be documented in `.review/README.md`.

## 6. Review metadata

Each review has one small metadata file, `review.md`, inside its review directory.

It MUST contain:

- a stable review ID;
- a title or purpose;
- a review status (`open` or `closed`);
- scope.

Recommended metadata:

- base reference (for example a commit or pull-request reference);
- type;
- reviewer;
- created date;
- RaC version (the protocol version the record was written against, for forward compatibility).

The review status is summary metadata only. It MUST NOT be used to track individual finding workflow. See Section 12.

Example:

```markdown
# R042 — Payment API Review

Status: open

Scope:
- src/payment/

Base: abc123
```

A review MAY be closed only when there are no outstanding findings (Section 12).

## 7. Findings

Each finding MUST have a stable ID within its review.

Recommended local ID format:

```text
F001
F002
```

The canonical, globally unique ID is `R042-F001`, derived from the review directory and the finding filename. External references SHOULD use the canonical ID.

A finding SHOULD contain:

- ID;
- title;
- State;
- severity when applicable;
- location when applicable;
- observation;
- recommendation when applicable;
- a Decision Log;
- an Implementation Log when applicable;
- a Verification Log when applicable.

Acceptance criteria MAY be recorded within a finding when they help define a decision or verification.

### 7.1 Finding format

A finding is stored in its own file under the review's `findings/` directory.

The canonical finding structure is:

```text
Finding
├── State
├── Observation
├── Recommendation
├── Decision Log
├── Implementation Log
└── Verification Log
```

Example:

```markdown
# F001 — Missing request timeout

State: decision=accepted, implementation=implemented, verification=verified

Severity: medium

Location:
src/payment/client.ts

## Observation

The outbound request has no explicit timeout.

## Recommendation

Configure an explicit timeout.

## Decision Log

### D001 — accepted

**Date:** 2026-09-14
**Actor:** Author

Rationale:
A timeout will be added to all outbound requests.

## Implementation Log

### I001 — implemented

**Date:** 2026-09-14
**Actor:** Author

Evidence:
- Commit: abc123
- Files:
  - src/payment/client.ts

## Verification Log

### V001 — verified

**Date:** 2026-09-14
**Actor:** Reviewer

Evidence:
- Commit: abc123
- Check: npm test -- payment-client
- Result: pass
```

Findings are independently writable. See Section 13 for merge-conflict design rules.

## 8. Lifecycle model

### 8.1 Independent dimensions

A finding has three independent lifecycle dimensions:

```text
Decision
Implementation
Verification
```

The dimensions are independent. Any combination MAY occur:

- an accepted finding MAY remain unimplemented;
- an implemented finding MAY remain unverified;
- failed verification does not erase or invalidate implementation history.

The specification does not define a transition matrix. Agents MUST NOT reason about legal state transitions or workflow graphs.

### 8.2 Decision

The disposition of a finding, recorded in the Decision Log. Recommended values:

```text
pending
accepted
alternative
rejected
accepted_risk
deferred
not_applicable
```

### 8.3 Implementation

The implementation work performed for a finding, recorded in the Implementation Log. Recommended values:

```text
pending
implemented
```

### 8.4 Verification

The verification result for a finding, recorded in the Verification Log. Recommended values:

```text
pending
verified
failed
not_required
```

### 8.5 State

`State` is the derived, materialised representation of the current finding condition.

It MUST represent the current Decision, Implementation, and Verification values:

```text
State: decision=<value>, implementation=<value>, verification=<value>
```

`State` MUST be derived from the finding record and its lifecycle logs. The lifecycle logs remain the authoritative historical record.

`State` MUST NOT become the authoritative history. It is a convenience view so that agents can determine the current finding condition without processing the complete historical context when a valid `State` is available.

`State` does not require a central registry or database. It does not introduce unnecessary duplication of historical information; it reflects only the current values.

## 9. Roles and ownership

Ownership is defined by section, not by entire file.

### 9.1 Core roles

The core RaC role model defines only two roles:

- **Author** — the creator of the artifacts under review, who implements accepted findings.
- **Reviewer** — the reviewer of artifacts, who creates findings, participates in finding decisions, and verifies remediation.

There is no separate `Artifact Owner` or `Verifier` role. A policy MAY require independent verification by a different reviewer without introducing a separate `Verifier` role.

### 9.2 Reviewer owns Observation

The reviewer creates and owns:

```text
Observation
Recommendation
Severity
Location
```

The observation SHOULD NOT be silently rewritten by the author or any other role. If the observation is incorrect, add clarification or superseding information rather than silently replacing it.

### 9.3 Decision Log

Both the author and the reviewer MAY contribute entries to the Decision Log.

The author decides the disposition of the finding. The reviewer participates in finding decisions. The reviewer MUST NOT unilaterally make a decision on behalf of the author.

### 9.4 Implementation Log

Implementation Log entries are authored by the `Author`.

### 9.5 Verification Log

Verification Log entries are authored by the `Reviewer`.

The same person or agent MAY perform different roles at different times, but MUST respect the boundaries of the current operation.

## 10. Lifecycle logs

Each lifecycle dimension has its own append-only log:

```text
Decision Log
Implementation Log
Verification Log
```

Logs contain decision-relevant information, not discussion transcripts.

Each log entry MUST have:

- a stable identifier;
- an actor;
- a date;
- content.

### 10.1 Decision Log

Both `Author` and `Reviewer` can contribute to the Decision Log.

Recommended local ID format: `D001`, `D002`, ...

Example entry:

```markdown
### D001 — accepted

**Date:** 2026-09-14
**Actor:** Author

Rationale:
A timeout will be added to all outbound requests.
```

### 10.2 Implementation Log

Implementation Log entries are authored by the `Author`.

Recommended local ID format: `I001`, `I002`, ...

Implementation evidence SHOULD reference Git-native artifacts such as commits, pull requests, or changed files.

Example entry:

```markdown
### I001 — implemented

**Date:** 2026-09-14
**Actor:** Author

Evidence:
- Commit: abc123
- Files:
  - src/payment/client.ts
```

### 10.3 Verification Log

Verification Log entries are authored by the `Reviewer`.

Recommended local ID format: `V001`, `V002`, ...

Example entry:

```markdown
### V001 — verified

**Date:** 2026-09-14
**Actor:** Reviewer

Evidence:
- Commit: abc123
- Check: npm test -- payment-client
- Result: pass
```

### 10.4 Append-only and immutability

Historical log entries MUST NOT be rewritten as part of normal lifecycle progression.

To record new information, append a new entry with a new stable identifier. Do not edit an existing entry.

Failed verification does not erase or invalidate implementation history. A later `verified` entry supersedes a prior `failed` entry in the derived `State`, without rewriting it.

## 11. Closure semantics

Closure is derived deterministically from the finding's independent lifecycle dimensions. The specification does not define a workflow state machine.

A finding MAY be considered closed when:

1. a final decision exists (the Decision value is not `pending`); and
2. if the decision requires implementation, the Implementation is recorded as `implemented`; and
3. if verification is required, the Verification is recorded as `verified`.

Verification is required when the decision is `accepted` or `alternative`, unless a policy declares verification `not_required`.

Examples.

Accepted, implemented, and verified:

```text
State: decision=accepted, implementation=implemented, verification=verified
```

Rejected:

```text
State: decision=rejected, implementation=pending, verification=not_required
```

Accepted risk:

```text
State: decision=accepted_risk, implementation=pending, verification=not_required
```

Deferred:

```text
State: decision=deferred, implementation=pending, verification=not_required
```

Not applicable:

```text
State: decision=not_applicable, implementation=pending, verification=not_required
```

Not closed:

```text
State: decision=pending, implementation=pending, verification=pending
State: decision=accepted, implementation=pending, verification=pending
State: decision=accepted, implementation=implemented, verification=pending
State: decision=accepted, implementation=implemented, verification=failed
```

- An `accepted` finding with pending implementation is not closed.
- An implemented finding with pending verification is not closed when verification is required.
- Failed verification prevents closure when verification is required.

A deferred finding is no longer outstanding for the current review. If it needs future action, create a new review or issue reference.

## 12. Outstanding findings

A finding is outstanding when it is not closed per Section 11.

A review has no outstanding findings when every finding belonging to the review is closed.

The review status MUST NOT duplicate a manually maintained count of findings. Agents SHOULD derive outstanding findings by inspecting finding files and their lifecycle logs.

A review MAY be closed only when no outstanding findings exist.

## 13. Merge conflict design rules

Implement the following design constraints.

### 13.1 One finding per file

Never store multiple findings in one mutable document.

### 13.2 Creating a finding must not modify existing findings

Creating a new finding file MUST NOT require updating any existing finding file.

### 13.3 Avoid global indexes

Creating or closing a review, or creating, closing, or verifying a finding, MUST NOT require updating `.review/README.md` or any other global index. The filesystem is the primary index.

### 13.4 Minimize shared files

The only shared review-level file SHOULD normally be `review.md`. Agents SHOULD rarely need to modify it.

## 14. Append-oriented updates

Prefer appending log entries to rewriting a finding. Do not rewrite an entire finding when only one owned section changes.

For example, the author updates only the Implementation Log and the derived `State`:

```markdown
## Implementation Log

### I001 — implemented

**Date:** 2026-09-14
**Actor:** Author

Evidence:
- Commit: abc123
```

The reviewer updates only the Verification Log and the derived `State`:

```markdown
## Verification Log

### V001 — verified

**Date:** 2026-09-14
**Actor:** Reviewer

Evidence:
- Commit: abc123
- Check: npm test -- payment-client
- Result: pass
```

This minimizes overlapping edits and merge conflicts.

## 15. Discussion

RaC does not define a feedback-thread primitive.

Discussion MAY happen:

- in pull requests;
- in issues;
- in external collaboration tools;
- in comments associated with findings.

RaC SHOULD record the final decision and verification result, not reproduce the entire conversation.

## 16. Markdown as the canonical source

Markdown MUST be the canonical source format.

Do not require agents to maintain both Markdown and machine-readable representations.

If machine-readable output is required, generate it from Markdown:

```text
Markdown → parser → JSON
```

Do not use `Markdown + manually synchronized JSON`.

### 16.1 Markdown-to-schema projection

The optional schemas in `schemas/` are machine-readable projections of the Markdown records. This section defines how each projection is derived.

Review metadata (`schemas/review.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# R042 — Title` heading | `id` (`R042`) and `title` (text after the em dash) |
| `Type: Code` | `type` |
| `Status: open` / `Status: closed` | `status` |
| `Scope:` block | `scope` |
| `Base: abc123` | `base` |
| `RaC version: v0.5.3` | `rac_version` |
| `Closed: <date>` | `closed_date` |
| Optional `## Review Outcome` section content | `outcome` |

Finding (`schemas/finding.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# F001 — Title` heading | `id` (`F001`) and `title` (text after the em dash) |
| `State: decision=..., implementation=..., verification=...` | `state` (object with `decision`, `implementation`, `verification`) |
| `Severity: medium` | `severity` |
| `Location:` block | `location` |
| `## Observation` content | `observation` |
| `## Recommendation` content | `recommendation` |
| `## Decision Log` entries | `decision_log` (array of entries with `id`, `value`, `date`, `actor`, and `content`) |
| `## Implementation Log` entries | `implementation_log` (array of entries with `id`, `value`, `date`, `actor`, and `content`) |
| `## Verification Log` entries | `verification_log` (array of entries with `id`, `value`, `date`, `actor`, and `content`) |

Fields that appear in the Markdown but have no schema property are not projected.

## 17. Progressive loading

Agents MUST load the minimum information necessary for the requested operation.

Recommended loading sequence:

```text
Repository instructions
        ↓
Target review metadata
        ↓
Target finding
        ↓
Relevant source files
```

Avoid loading the entire specification, the entire skill, all reviews, all findings, all history, or the entire repository unless the task actually requires them.

## 18. Agent operations

Define operations with minimal write scope.

### 18.1 Review

Role: Reviewer.

Input: source files, review scope, relevant instructions.

Output: new finding files.

Do not modify unrelated findings.

### 18.2 Implement

Role: Author.

Input: target finding, relevant source files.

Output: source changes.

Append an entry to the target finding's Implementation Log and update its derived `State` when explicitly authorized to record implementation evidence.

Do not modify the Observation or other reviewer-owned sections.

### 18.3 Decide

Role: Author or Reviewer.

Input: target finding, disposition response.

Output: a new Decision Log entry and an updated derived `State`.

### 18.4 Verify

Role: Reviewer.

Input: target finding, implementation, relevant tests.

Output: a new Verification Log entry with evidence and an updated derived `State`.

Only modify the target finding.

### 18.5 Close

Input: all finding states.

Output: review status update if appropriate.

Before closing a review:

1. enumerate finding files;
2. check each finding's derived `State`;
3. if any finding is outstanding (Section 12), do not close the review;
4. otherwise, close the review.

## 19. Multiple reviews and relationships

A repository MAY contain multiple independent reviews.

A new review SHOULD be created for a genuinely new purpose or scope.

Reviews MAY reference other reviews. Recommended relationship types:

```text
Follows
Verifies
Supersedes
Expands
Related To
```

Historical review records SHOULD NOT be rewritten merely because a later review exists.

## 20. Conformance

A RaC implementation conforms to v0.5.3 when it:

1. supports multiple review records;
2. provides stable review and finding IDs;
3. stores each finding as an independent file;
4. defines two core roles, `Author` and `Reviewer`;
5. preserves role ownership boundaries over observations, decisions, implementation, and verification;
6. separates discussion from the review record;
7. distinguishes recommendations from decisions;
8. distinguishes implementation from verification;
9. records lifecycle progression in append-only Decision, Implementation, and Verification logs;
10. derives `State` from the finding record and lifecycle logs;
11. provides a human-readable Markdown representation.

## 21. Fundamental invariant

> The reviewer observes and verifies. The author implements. Both record decisions.

The same human or agent MAY perform multiple roles at different times, but MUST NOT collapse ownership boundaries within a single operation.