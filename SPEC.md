# Review as Code Specification

## Version

**RaC v0.3.1**

## 1. Purpose

Review as Code (RaC) defines a Git-native, human-readable protocol for recording and managing reviews of repository artifacts.

RaC applies to source code, documentation, specifications, requirements, architecture, configuration, and other reviewable artifacts.

The goals of RaC are to:

- make review findings durable and version controlled;
- support multiple reviews in the same repository;
- support multiple rounds within a review;
- remain easy for humans to read and edit;
- provide explicit semantics for AI agents;
- separate review, discussion, implementation, and verification;
- support git-managed feedback between reviewers and implementers.

## 2. Normative language

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are normative.

## 3. Core principles

### 3.1 Human-friendly first

Review records MUST be human-readable. Markdown is the canonical default representation.

### 3.2 Git native

Review records SHOULD be version controlled alongside the work they review.

### 3.3 Review independence

The reviewer owns the review artifact. The author or implementer owns the reviewed artifacts.

A reviewer MUST NOT modify artifacts under review while acting as reviewer.

An implementer MUST NOT modify reviewer-owned review records while acting as implementer.

Feedback items are owned by their author. A reviewer MUST NOT modify implementer-authored feedback items, and an implementer MUST NOT modify reviewer-authored feedback items. See Section 8.5.

### 3.4 Discussion is separate

The review record MUST NOT be used as a conversation transcript.

Discussion MAY occur in pull requests, issues, chat, meetings, or other systems.

The review record SHOULD capture the resulting decision and rationale concisely.

Structured feedback records (Section 8.5) are distinct from conversation chatter. Feedback records are durable, git-managed, decision-relevant exchanges between reviewers and implementers and are stored outside the review record. Conversational discussion that is not decision-relevant remains external to the repository.

### 3.5 Explicit decisions

A recommendation is not permission to modify an artifact.

An actionable change requires an explicit accepted decision or explicit instruction from an authorized human.

### 3.6 Independent verification

Implementation and verification are distinct operations.

An implementer MUST NOT mark a finding as verified on behalf of the reviewer.

### 3.7 Stable identity

Reviews and findings MUST have stable identifiers.

Identifiers MUST NOT change when wording, status, or implementation changes.

## 4. Terminology

### Review

A durable assessment process covering one or more artifacts for a defined purpose and scope.

A review MAY contain multiple review rounds.

### Review round

One iteration of review, discussion/decision, implementation, and verification within an open review.

A new round does not create a new review.

### Finding

An observation, concern, recommendation, or other review result recorded within a review.

### Decision

The agreed outcome for a finding.

### Implementation

A change to reviewed artifacts intended to satisfy an accepted decision.

### Verification

An independent assessment that implementation satisfies the decision and applicable acceptance criteria.

### Scope

The set of reviewed artifacts covered by a review, expressed as file paths, directory paths, or other artifact references.

When a review covers a changeset, the scope SHOULD include a commit or pull-request reference so the review remains reproducible.

### Purpose

The reason or objective for conducting a review, expressed as free text.

### Acceptance criteria

Observable conditions that must be satisfied before an implementation satisfies a decision.

### Author

The person or agent who created or maintains the reviewed artifacts. Synonymous with implementer when acting in an implementation capacity.

### Review owner

The person or agent currently authorized to write review records for a given review. Typically the reviewer.

### Terminal outcome

A finding state that represents a final disposition requiring no further action within the current review.

Terminal outcomes are: Verified, Rejected, Accepted Risk, Deferred, Not Applicable, Stale.

All other states (Open, Discussing, Accepted, Accepted Alternative, In Progress, Resolved) are non-terminal.

### Reviewed artifacts

The code, documents, specifications, or other artifacts being assessed.

### Feedback record

A single git-managed, append-only message in which a reviewer or implementer exchanges substantive clarification, questions, responses, or confirmation about a finding.

### Feedback item

A feedback record within a feedback thread. Each feedback item has a stable identifier within its thread.

### Feedback thread

The ordered set of feedback items associated with a single finding. A thread spans review rounds.

### Feedback author

The person or agent who wrote a feedback item. The author owns their own feedback items.

## 5. Repository model

A repository MAY contain many reviews.

The default layout is:

```text
.review/
├── README.md
├── reviews/
│   ├── R001.md
│   ├── R002.md
│   └── R003.md
└── feedback/
    ├── R001-F001.md
    └── R003-F002.md
```

Repositories MAY define another layout in `.review/README.md`.

A single global review file SHOULD NOT be used to represent multiple unrelated reviews.

Before allocating a new review ID, an agent SHOULD scan the existing review records and take the next sequential unused ID (for example, the next `R###` number). The allocation convention MAY be documented in `.review/README.md`.

### 5.1 Review README

`.review/README.md` describes the repository's review layout and conventions.

It SHOULD document:

- the review layout and the paths of review records;
- the feedback layout and the paths of feedback threads;
- the review ID convention (for example `R###`);
- the finding ID convention (for example `R###-F###`);
- the feedback item ID convention (for example `R###-F###-FB###`);
- how rounds are represented;
- the controlled status vocabulary when a subset is used;
- the severity vocabulary when a subset is used;
- the active reviews and closed reviews when useful.

It MAY override the default layout described above.

It MUST remain human-readable and SHOULD be readable by agents for discovery.

Example:

```markdown
# Review as Code

Reviews are stored under `reviews/` in this repository.

## Layout

Each review is a single Markdown file under `reviews/`.
- IDs follow `R###` (for example `R042`).
- Findings follow `R###-F###` (for example `R042-F001`).
- Rounds are represented as sections within the review file.

Feedback threads are stored under `feedback/`.
- Thread filenames follow `R###-F###` (for example `R042-F001.md`).
- Feedback items follow `R###-F###-FB###` (for example `R042-F001-FB001`).

## Status

This repository uses the full RaC vocabulary from the specification.

## Severity

Critical, High, Medium, Low, Informational.

## Active reviews

- R042 — Payment API
- R055 — Multi-Round Service
```

## 6. Review records

Each review record MUST contain:

- stable review ID;
- title or purpose;
- review status (`Open` or `Closed`);
- scope;
- findings or an explicit statement that no findings were produced.

Recommended metadata:

- type;
- reviewer;
- created date;
- related review;
- current round;
- RaC version (the protocol version the record was written against, for forward compatibility).

Example:

```markdown
# R042 — Payment API Review

**Type:** Code
**Status:** Open
**Scope:** `src/payment/`
**Reviewer:** Alice
**Created:** 2026-09-06
**RaC version:** v0.3.0

## Findings
```

## 7. Review rounds

A review MAY contain one or more rounds.

A round represents a review iteration, not a separate review identity.

A review remains open until the reviewer closes it.

Example:

```text
R042
├── Round 1 — Initial review
├── Round 2 — Follow-up
└── Round 3 — Final verification
```

A review MAY have verified findings while remaining open because other findings require further work.

New findings discovered during a later round MUST receive new stable finding IDs.

Previously recorded findings SHOULD retain their original IDs.

A round MAY be represented as metadata, a dedicated section, or another human-readable convention defined by the repository.

## 8. Findings

Each finding MUST have a stable ID within its review.

Recommended format:

```text
R042-F001
R042-F002
```

A finding SHOULD contain:

- ID;
- title;
- severity when applicable;
- status;
- location when applicable;
- description;
- recommendation when applicable;
- decision when known;
- acceptance criteria when actionable;
- verification evidence when verified.

Verification evidence SHOULD be reproducible, for example by recording the commands, test names, or commit references used to verify.

Example:

```markdown
### R042-F001 — Missing request timeout

**Round:** 1
**Severity:** Medium
**Status:** Open
**Location:** `src/payment/client.ts`

The outbound request has no explicit timeout.

**Recommendation**

Configure an explicit timeout.

**Acceptance**

- [ ] A timeout is configured.
- [ ] Timeout behavior is tested.
```

### 8.5 Review feedback

The feedback loop allows reviewers and implementers to exchange substantive clarification, questions, responses, and confirmation through git-managed feedback records.

A feedback thread MUST be stored outside the review record, in the repository's feedback layout (Section 5).

A feedback thread is associated with exactly one finding and MUST use a stable identifier derived from the finding.

Recommended thread filename format:

```text
.review/feedback/R042-F001.md
```

Each feedback item in a thread MUST have a stable identifier within the thread.

Recommended feedback item ID format:

```text
R042-F001-FB001
R042-F001-FB002
```

Feedback items MUST be append-only. New items MUST receive new stable IDs. Previously recorded items MUST retain their original IDs and content.

A feedback item SHOULD contain:

- ID;
- author role;
- date;
- content.

A feedback item MAY contain an author identity (for example a name or agent ID) alongside the role.

Recommended item heading format and kinds:

```text
## FB001 — Clarification
## FB002 — Response
## FB003 — Report
## FB004 — Confirmation
```

Recommended kinds are: Clarification, Response, Report, and Confirmation. Report covers progress and completion reports from the implementer under the handoff rule in Section 17.2.

Feedback participants include reviewers, implementers, and verifiers. A repository MAY extend the role vocabulary.

Example:

```markdown
# R042-F001 — Feedback thread

**Finding:** [R042-F001](../reviews/R042.md) — Missing request timeout

## FB001 — Clarification

**Author:** Reviewer
**Date:** 2026-09-07

Does the shared HTTP client already expose a timeout option, or must we add one?

## FB002 — Response

**Author:** Implementer
**Identity:** Bob
**Date:** 2026-09-07

The shared client exposes `timeoutMs`; we can set it at the call site.

## FB003 — Confirmation

**Author:** Reviewer
**Date:** 2026-09-07

Setting it at the call site satisfies the recommendation.
```

Feedback items are owned by their author. A reviewer MUST NOT modify implementer-authored feedback items, and an implementer MUST NOT modify reviewer-authored feedback items (Section 3.3).

Feedback feeds decisions but is not itself a decision. A decision MUST be recorded in the review record by the review owner (Section 11). Feedback does not replace external discussion for agreement (Section 3.4).

If participants cannot reach agreement through feedback, the review owner or a designated arbiter MAY record a decision with a rationale that includes a summary of the exchange (Section 13.10).

## 9. Severity

Recommended severity values are:

- Critical
- High
- Medium
- Low
- Informational

Severity communicates impact and does not force acceptance.

## 10. Finding states and decisions

Recommended states are:

- Open
- Discussing
- Accepted
- Accepted Alternative
- Rejected
- Accepted Risk
- Deferred
- In Progress
- Resolved
- Verified
- Not Applicable
- Stale

Repositories MAY define a smaller controlled vocabulary.

The following table defines the default legal transitions between states. It is the recommended baseline; repositories MAY delegate transition control to a narrower set defined in `.review/README.md`.

| Current state | Legal transitions |
|---------------|-------------------|
| Open | Discussing; any decision outcome (Accepted, Accepted Alternative, Rejected, Accepted Risk, Deferred, Not Applicable, Stale) |
| Discussing | Open; any decision outcome |
| Accepted | Accepted Alternative; In Progress |
| Accepted Alternative | Accepted; In Progress |
| In Progress | Resolved |
| Resolved | In Progress (rework); Verified |
| Verified | terminal within the current review; reopening governed by Section 13.11 |
| Rejected, Accepted Risk, Deferred, Not Applicable, Stale | terminal within the current review; reopening governed by Section 13.11 |

Opening or participating in a feedback thread (Section 8.5) MAY be reflected by setting the finding status to Discussing.

A Stale finding reflects a changed underlying condition and is not equivalent to verified. A Stale finding MAY be reopened, with the review owner's authorization, if the condition changes such that the finding applies again; reopening is governed by Section 13.11.

### Open

The finding has been raised and has no final decision.

### Discussing

The finding is being discussed and no final decision exists.

### Accepted

The recommended change or concern has been accepted.

### Accepted Alternative

A different solution has been accepted.

### Rejected

No change will be made in response to the finding.

### Accepted Risk

The concern is acknowledged and intentionally accepted without remediation.

### Deferred

The finding is valid but implementation is intentionally postponed.

### In Progress

Implementation is underway.

### Resolved

Implementation has been completed but independent verification remains.

### Verified

The reviewer has verified that the decision and acceptance criteria are satisfied.

### Not Applicable

The finding does not apply.

### Stale

The underlying condition changed such that the finding no longer applies. Stale is not equivalent to verified.

## 11. Decision recording

Decisions SHOULD record:

- outcome;
- concise rationale where useful;
- alternative approach when applicable.

The review record SHOULD record the result of discussion, not reproduce the discussion.

Example:

```markdown
**Decision:** Accepted Alternative

The timeout will be configured in the shared HTTP client.

**Rationale:** This provides consistent behavior for all outbound clients.
```

## 12. Ownership

### Reviewer

When acting as reviewer:

- READ reviewed artifacts;
- WRITE review artifacts;
- WRITE feedback items authored by the reviewer;
- MUST NOT modify reviewed artifacts;
- MUST NOT modify feedback items authored by another party.

### Implementer

When acting as implementer:

- READ reviewed artifacts and applicable review artifacts;
- WRITE reviewed artifacts;
- WRITE feedback items authored by the implementer;
- MUST NOT modify reviewer-owned review artifacts;
- MUST NOT modify feedback items authored by another party.

### Verifier

When acting as verifier:

- READ reviewed artifacts and review artifacts;
- WRITE verification results to review artifacts;
- WRITE feedback items authored by the verifier;
- MUST NOT modify reviewed artifacts merely to make verification pass;
- MUST NOT modify feedback items authored by another party.

The same person or agent MAY perform different roles at different times, but MUST respect the boundaries of the current operation.

The terms "author" and "implementer" are used interchangeably throughout this specification when referring to the person or agent who owns and modifies the reviewed artifacts.

## 13. Workflow

### 13.1 Create or open a review

A review context is created for a defined scope and purpose.

### 13.2 Review round

The reviewer inspects the reviewed artifacts and records findings.

### 13.3 Discussion

Participants discuss findings outside the review artifact when agreement is required.

### 13.4 Feedback exchange

Reviewers and implementers MAY exchange substantive clarification, questions, responses, and confirmation through feedback records (Section 8.5).

Feedback items are appended to the finding's feedback thread. Each item is owned by its author.

Feedback does not replace the decision. The resulting decision is still recorded in the review artifact by the review owner.

### 13.5 Decision

The resulting decision is recorded in the review artifact by the reviewer or another explicitly authorized review owner.

### 13.6 Implementation

The author or implementer reads accepted decisions and modifies the reviewed artifacts.

The implementer MUST NOT silently reinterpret an unaccepted finding as accepted.

### 13.7 Verification

The reviewer independently inspects the resulting artifacts and records verification evidence.

### 13.8 Additional rounds

If further work is required, the review continues with another round.

### 13.9 Closure

The reviewer MAY close the review when all applicable findings have terminal outcomes and the review purpose is complete.

On closure, the reviewer sets the record status to `Closed` and SHOULD record a closure date and a concise rationale for closure. The reviewer MAY include an optional outcome summary section (for example `## Review Outcome`) summarizing the outcome.

Terminal outcomes are defined in Section 4 and include: Verified, Rejected, Accepted Risk, Deferred, Not Applicable, and Stale.

### 13.10 Conflict resolution

If participants cannot reach agreement on a finding, the review owner or a designated arbiter MAY record a decision with a rationale that includes a summary of the disagreement.

The finding progresses once an authorized decision is recorded, regardless of ongoing disagreement.

### 13.11 Decision revocation

An accepted finding MAY be reopened if new information makes the decision infeasible.

Revocation requires explicit authorization from the review owner.

The finding returns to Discussing or Open with a note referencing the revoked decision.

Implementation MUST NOT proceed on revoked decisions.

### 13.12 Reviewer handoff

A review MAY be transferred to a new reviewer.

Transfer requires explicit authorization, such as a commit message, pull request approval, or review record annotation.

The review record SHOULD document the transfer with date and rationale.

## 14. Workflow model

```text
Author changes artifacts
        |
        v
Reviewer creates/opens review
        |
        v
Review round
        |
        v
Findings
        |
        v
External discussion  <-->  Feedback loop (clarify / respond / confirm)
        |
        v
Decision recorded
        |
        v
Implementer changes artifacts
        |
        v
Reviewer verifies
        |
   +----+----+
   |         |
more work   complete
   |         |
   v         v
next round  close review
```

## 15. Multiple reviews

A repository MAY contain multiple independent reviews.

Examples:

- architecture review;
- implementation review;
- security review;
- documentation review;
- final verification review.

A new review SHOULD be created for a genuinely new purpose or scope.

A new round SHOULD be used for continued iteration within the same review.

## 16. Review relationships

Reviews MAY reference other reviews.

Recommended relationship types:

- Follows
- Verifies
- Supersedes
- Expands
- Related To

Example:

```markdown
**Related review:** R042
**Relationship:** Verifies
```

Historical review records SHOULD NOT be rewritten merely because a later review exists.

## 17. Agent interoperability

Agents MUST distinguish:

```text
Observation
Recommendation
Decision
Implementation
Verification
```

These concepts are not interchangeable.

Agents MUST determine their current operation and ownership boundary before writing.

"Silently" means without recording a rationale or without a human-visible notification. Status changes to the review record MUST include a rationale or reference to an external decision.

Agents MUST NOT:

- implement an Open or Discussing finding without explicit authorization;
- modify reviewer-owned records while acting as implementer;
- modify reviewed artifacts while acting as reviewer;
- claim verification without evidence;
- silently close a review;
- modify feedback items authored by another party (Section 8.5).

### 17.1 Discovery

Before operating, an agent SHOULD:

1. identify which review is applicable to the requested scope and purpose;
2. determine the current review round;
3. identify the agent's assigned role and corresponding ownership boundary;
4. read applicable review records and reviewed artifacts;
5. consult the machine-readable schema where available for validation.

### 17.2 Handoff between agents

When one agent completes an operation, the review record SHOULD be updated before another agent operates.

Finding status changes are recorded by the review owner. An implementing agent MUST NOT write the review record; it reports progress and completion through its own feedback items (Section 8.5).

The review owner SHOULD record implementer-reported status changes promptly.

A verifying agent SHOULD read the current finding status and decision before verifying.

State transitions SHOULD be documented by the review owner in the review record.

## 18. Human-readable format

Markdown is the canonical default format.

Machine-readable representations MAY be generated for validation or automation but SHOULD NOT replace the human-readable review record.

### 18.1 Markdown-to-schema projection

The optional schemas in `schemas/` are machine-readable projections of the Markdown records. This section defines how each projection is derived.

Review record (`schemas/review.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# R042 — Title` heading | `id` (`R042`) and `title` (text after the em dash) |
| `**Type:**` | `type` |
| `**Status:**` | `status` |
| `**Scope:**` | `scope` |
| `**Current Round:**` | `current_round` (integer) |
| `**RaC version:**` | `rac_version` |
| Closure: record status `Closed` with `**Closed:** <date>` | `status: "Closed"` and `closed_date` |
| Optional `## Review Outcome` section content | `outcome` |
| `### R042-F001 — Title` heading | finding `id` (`R042-F001`) and `title` |
| Finding `**Round:**` | finding `round` (integer) |
| Finding `**Severity:**` | finding `severity` |
| Finding `**Status:**` | finding `status` |
| Finding `**Location:**` | finding `location` |
| Finding description paragraph | finding `description` |
| `**Recommendation**` section | finding `recommendation` |
| `**Decision:**` line | finding `decision` |
| `**Acceptance**` checkbox list | finding `acceptance` (array of item text without the `- [ ]` markers) |
| `**Verification**` / `**Evidence**` content | finding `verification` |

Feedback thread (`schemas/feedback.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# R042-F001 — Feedback thread` heading | `review_id` (`R042`) and `finding_id` (`R042-F001`) |
| `## FB001 — Clarification` heading | item `id` (the full `R042-F001-FB001`, derived from the finding ID and the `FB###` number) and item `kind` (text after the em dash) |
| `**Author:**` line | item `author_role` |
| `**Identity:**` line (optional) | item `author` |
| `**Date:**` line | item `date` |
| Item content paragraph | item `content` |

Fields that appear in the Markdown but have no schema property (for example `**Reviewer:**`, `**Created:**`, and feedback links) are not projected.

## 19. Conformance

A RaC implementation conforms to v0.3.1 when it:

1. supports multiple review records;
2. provides stable review and finding IDs;
3. supports multiple rounds within a review;
4. preserves reviewer/reviewed-artifact ownership boundaries;
5. separates discussion from the review record;
6. distinguishes recommendations from decisions;
7. distinguishes implementation from verification;
8. provides a human-readable review representation;
9. supports git-managed feedback records between reviewers and implementers.

## 20. Fundamental invariant

> The reviewer reviews. The implementer implements. The reviewer verifies.

The same human or agent MAY perform multiple roles at different times, but MUST NOT collapse ownership boundaries within a single operation.
