# Review as Code Specification

## Version

**RaC v0.4.3**

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
- preserve role ownership over observations, decisions, and verification.

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

The reviewer records observations. The artifact owner records decisions. The verifier records verification.

A reviewer MUST NOT modify artifacts under review while acting as reviewer. An implementer MUST NOT modify reviewer-owned review records while acting as implementer. No role may silently rewrite another role's owned content. See Section 9.

### 3.5 Explicit decisions

A recommendation is not permission to modify an artifact.

An actionable change requires an explicit accepted decision or explicit instruction from an authorized human.

### 3.6 Independent verification

Implementation and verification are distinct operations.

An implementer SHOULD NOT verify their own implementation when independent verification is required.

### 3.7 Discussion is external

The review record MUST NOT be used as a conversation transcript.

Discussion MAY occur in pull requests, issues, chat, meetings, or other systems. RaC records the resulting decision and verification result, not the conversation.

### 3.8 Minimize shared mutable files

Findings MUST be stored so that most operations create one file or modify one file. Shared mutable files MUST be minimized to reduce merge conflicts. See Section 10.

## 4. Terminology

### Review

A durable assessment process covering one or more artifacts for a defined purpose and scope.

### Finding

An observation, concern, recommendation, or other review result recorded within a review. A finding is an independently addressable artifact.

### Observation

The reviewer's description of the finding, its location, severity, and recommendation.

### Decision

The agreed disposition for a finding, owned by the artifact owner.

### Implementation

A change to reviewed artifacts intended to satisfy an accepted decision.

### Verification

An independent assessment that implementation satisfies the decision and applicable acceptance criteria, recorded with evidence.

### Scope

The set of reviewed artifacts covered by a review, expressed as file paths, directory paths, or other artifact references.

When a review covers a changeset, the scope SHOULD include a commit or pull-request reference so the review remains reproducible.

### Purpose

The reason or objective for conducting a review, expressed as free text.

### Artifact owner

The person or agent who owns the reviewed artifacts and decides the disposition of findings. Synonymous with author or implementer when acting in an implementation capacity.

### Reviewer

The person or agent who records observations and findings for a review.

### Verifier

The person or agent who independently records verification results for a finding.

### Outstanding finding

A finding whose Status is `open`. See Section 11.

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
- the controlled status, decision, and verification vocabularies when a subset is used;
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

The review status is summary metadata only. It MUST NOT be used to track individual finding workflow. See Section 11.

Example:

```markdown
# R042 — Payment API Review

Status: open

Scope:
- src/payment/

Base: abc123
```

A review MAY be closed only when there are no outstanding findings (Section 11).

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
- status;
- severity when applicable;
- location when applicable;
- observation;
- recommendation when applicable;
- decision when known;
- verification result and evidence when verified.

Acceptance criteria MAY be recorded within a finding when they help define a decision or verification.

### 7.1 Finding format

A finding is stored in its own file under the review's `findings/` directory.

Example:

```markdown
# F001 — Missing request timeout

Status: open

Severity: medium

Location:
src/payment/client.ts

## Observation

The outbound request has no explicit timeout.

## Recommendation

Configure an explicit timeout.

## Decision

Pending.

## Verification

Pending.
```

Findings are independently writable. See Section 12 for merge-conflict design rules.

## 8. Finding lifecycle

A finding has exactly two statuses:

```text
Status:
- open
- closed
```

Status is the only workflow field on a finding. Everything else is represented separately.

### 8.1 Decision

The disposition of a finding, owned by the artifact owner. Recommended values:

```text
pending
accepted
alternative
rejected
accepted_risk
deferred
not_applicable
```

### 8.2 Verification

The independent verification result for a finding, owned by the verifier. Recommended values:

```text
pending
verified
failed
not_required
```

The specification does not define a transition matrix. Agents MUST NOT reason about legal state transitions or workflow graphs.

## 9. Ownership

Ownership is defined by section, not by entire file.

### 9.1 Reviewer owns Observation

The reviewer creates and owns:

```text
Observation
Recommendation
Severity
Location
```

The observation SHOULD NOT be silently rewritten by the artifact owner or any other role. If the observation is incorrect, add clarification or superseding information rather than silently replacing it.

### 9.2 Artifact owner owns Decision

The artifact owner decides the disposition of the finding and owns:

```text
Decision
Decision rationale
```

The reviewer MUST NOT unilaterally make a decision on behalf of the artifact owner.

### 9.3 Verifier owns Verification

The verifier records:

```text
Verification result
Evidence
Checks performed
```

Example:

```markdown
## Verification

Status: verified

Evidence:
- Commit: abc123
- Check: npm test
- Result: pass
```

The same person or agent MAY perform different roles at different times, but MUST respect the boundaries of the current operation.

## 10. Merge conflict design rules

Implement the following design constraints.

### 10.1 One finding per file

Never store multiple findings in one mutable document.

### 10.2 Creating a finding must not modify existing findings

Creating a new finding file MUST NOT require updating any existing finding file.

### 10.3 Avoid global indexes

Creating or closing a review, or creating, closing, or verifying a finding, MUST NOT require updating `.review/README.md` or any other global index. The filesystem is the primary index.

### 10.4 Minimize shared files

The only shared review-level file SHOULD normally be `review.md`. Agents SHOULD rarely need to modify it.

## 11. Outstanding findings

A finding is outstanding when its Status is `open`.

A review has no outstanding findings when every finding belonging to the review has Status `closed`.

The review status MUST NOT duplicate a manually maintained count of findings. Agents SHOULD derive outstanding findings by inspecting finding files.

Example:

```text
F001 → closed
F002 → closed
F003 → open

Result:
Outstanding findings exist.
```

Example:

```text
F001 → closed
F002 → closed
F003 → closed

Result:
No outstanding findings.
```

A review MAY be closed only when no outstanding findings exist.

## 12. Closure

A finding MAY be closed when:

1. a final decision exists; and
2. required verification has completed.

Examples.

Accepted and verified:

```text
Status: closed
Decision: accepted
Verification: verified
```

Rejected:

```text
Status: closed
Decision: rejected
Verification: not_required
```

Accepted risk:

```text
Status: closed
Decision: accepted_risk
Verification: not_required
```

Deferred:

```text
Status: closed
Decision: deferred
Verification: not_required
```

A deferred finding is no longer outstanding for the current review. If it needs future action, create a new review or issue reference.

## 13. Append-oriented updates

Prefer appending or changing isolated sections. Do not rewrite an entire finding when only one owned section changes.

For example, the artifact owner updates only:

```markdown
## Decision

Status: accepted

Rationale:
A timeout will be added to all outbound requests.
```

The verifier updates only:

```markdown
## Verification

Status: verified

Evidence:
- Commit: abc123
- Check: npm test
- Result: pass
```

This minimizes overlapping edits and merge conflicts.

## 14. Discussion

RaC does not define a feedback-thread primitive.

Discussion MAY happen:

- in pull requests;
- in issues;
- in external collaboration tools;
- in comments associated with findings.

RaC SHOULD record the final decision and verification result, not reproduce the entire conversation.

## 15. Markdown as the canonical source

Markdown MUST be the canonical source format.

Do not require agents to maintain both Markdown and machine-readable representations.

If machine-readable output is required, generate it from Markdown:

```text
Markdown → parser → JSON
```

Do not use `Markdown + manually synchronized JSON`.

### 15.1 Markdown-to-schema projection

The optional schemas in `schemas/` are machine-readable projections of the Markdown records. This section defines how each projection is derived.

Review metadata (`schemas/review.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# R042 — Title` heading | `id` (`R042`) and `title` (text after the em dash) |
| `Type: Code` | `type` |
| `Status: open` / `Status: closed` | `status` |
| `Scope:` block | `scope` |
| `Base: abc123` | `base` |
| `RaC version: v0.4.3` | `rac_version` |
| `Closed: <date>` | `closed_date` |
| Optional `## Review Outcome` section content | `outcome` |

Finding (`schemas/finding.schema.json`):

| Markdown element | Schema property |
|------------------|-----------------|
| `# F001 — Title` heading | `id` (`F001`) and `title` (text after the em dash) |
| `Status: open` / `Status: closed` | `status` |
| `Severity: medium` | `severity` |
| `Location:` block | `location` |
| `## Observation` content | `observation` |
| `## Recommendation` content | `recommendation` |
| `## Decision` content | `decision` and, when a `Rationale:` is present, `decision_rationale` |
| `## Verification` content | `verification` and, when evidence is present, `evidence` |

Fields that appear in the Markdown but have no schema property are not projected.

## 16. Progressive loading

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

## 17. Agent operations

Define operations with minimal write scope.

### 17.1 Review

Input: source files, review scope, relevant instructions.

Output: new finding files.

Do not modify unrelated findings.

### 17.2 Implement

Input: target finding, relevant source files.

Output: source changes.

Do not modify the finding unless explicitly authorized to record implementation evidence.

### 17.3 Decide

Input: target finding, artifact owner response.

Output: an update to the Decision section only.

### 17.4 Verify

Input: target finding, implementation, relevant tests.

Output: verification result and evidence.

Only modify the target finding.

### 17.5 Close

Input: all finding statuses.

Output: review status update if appropriate.

Before closing a review:

1. enumerate finding files;
2. check each finding Status;
3. if any finding is open, do not close the review;
4. otherwise, close the review.

## 18. Multiple reviews and relationships

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

## 19. Conformance

A RaC implementation conforms to v0.4.3 when it:

1. supports multiple review records;
2. provides stable review and finding IDs;
3. stores each finding as an independent file;
4. preserves role ownership boundaries over observations, decisions, and verification;
5. separates discussion from the review record;
6. distinguishes recommendations from decisions;
7. distinguishes implementation from verification;
8. represents finding status as `open | closed`;
9. derives review completion from finding statuses;
10. provides a human-readable Markdown representation.

## 20. Fundamental invariant

> The reviewer observes. The artifact owner decides. The verifier verifies.

The same human or agent MAY perform multiple roles at different times, but MUST NOT collapse ownership boundaries within a single operation.
