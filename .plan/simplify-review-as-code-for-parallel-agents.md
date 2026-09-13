# Review as Code Simplification Implementation Plan

## Objective

Simplify the Review as Code (RaC) specification and implementation to:

1. Minimize merge conflicts between humans and agents.
2. Reduce agent context and token costs.
3. Enable independent parallel work on findings.
4. Remove unnecessary workflow state.
5. Preserve the core RaC principles:

   * stable identity,
   * explicit decisions,
   * role separation,
   * independent verification,
   * Git-native history,
   * human-readable artifacts.

The target design is intentionally small.

---

# 1. Target Architecture

Replace the current review-centric mutable document model with independently writable finding artifacts.

## Target directory structure

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

Each finding MUST be independently addressable and independently writable.

Do not introduce a global mutable review index.

Do not require agents to update unrelated files when creating or updating a finding.

---

# 2. Core Data Model

The RaC model becomes:

```text
Review
│
├── Finding F001
│     ├── Observation
│     ├── Decision
│     └── Verification
│
├── Finding F002
│     ├── Observation
│     ├── Decision
│     └── Verification
│
└── Finding F003
      ├── Observation
      ├── Decision
      └── Verification
```

Remove rounds as a required data-model concept.

Remove feedback threads as a required data-model concept.

Remove the complex finding state machine.

Git history is the primary timeline.

---

# 3. Review Metadata

Create one small review metadata file.

## `review.md`

Example:

```markdown
# R042 — Payment API Review

Status: open

Scope:
- src/payment/

Base: abc123
```

Allowed review statuses:

```text
open
closed
```

The review status is summary metadata only.

It MUST NOT be used to track individual finding workflow.

A review MAY be closed only when there are no outstanding findings.

---

# 4. Finding Format

Each finding is stored in its own file.

Example:

## `findings/F001.md`

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

---

# 5. Finding Lifecycle

Replace the existing multi-state workflow with:

```text
Status:
- open
- closed
```

Everything else is represented separately.

## Decision values

Recommended values:

```text
pending
accepted
alternative
rejected
accepted_risk
deferred
not_applicable
```

## Verification values

Recommended values:

```text
pending
verified
failed
not_required
```

Do not define a transition matrix.

Do not implement legal state transitions.

Do not require agents to reason about workflow graphs.

---

# 6. Outstanding Finding Rule

Add this normative rule:

> A finding is outstanding when its Status is `open`.

> A review has no outstanding findings when every finding belonging to the review has Status `closed`.

The review status MUST NOT duplicate a manually maintained count of findings.

Agents SHOULD derive outstanding findings by inspecting finding files.

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

---

# 7. Ownership Model

Define ownership by section rather than by entire file.

## Reviewer owns Observation

The reviewer creates and owns:

```text
Observation
Recommendation
Severity
Location
```

The observation SHOULD NOT be silently rewritten by the artifact owner.

If the observation is incorrect, add clarification or superseding information rather than silently replacing it.

---

## Artifact Owner owns Decision

The artifact owner decides the disposition of the finding.

The artifact owner owns:

```text
Decision
Decision rationale
```

Possible decisions:

```text
accepted
alternative
rejected
accepted_risk
deferred
not_applicable
```

The reviewer MUST NOT unilaterally make a decision on behalf of the artifact owner.

---

## Verifier owns Verification

The verifier records:

```text
Verification result
Evidence
Relevant commit
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

The implementer SHOULD NOT verify their own implementation when independent verification is required.

---

# 8. Closure Rules

A finding MAY be closed when:

1. A final decision exists.
2. Required verification has completed.

Examples.

## Accepted and verified

```text
Status: closed
Decision: accepted
Verification: verified
```

## Rejected

```text
Status: closed
Decision: rejected
Verification: not_required
```

## Accepted risk

```text
Status: closed
Decision: accepted_risk
Verification: not_required
```

## Deferred

Choose one consistent policy.

Recommended:

```text
Status: closed
Decision: deferred
Verification: not_required
```

The finding is no longer outstanding for the current review.

If it needs future action, create a new review or issue reference.

---

# 9. Append-Oriented Updates

Prefer appending or changing isolated sections.

Do not rewrite the entire finding when only one responsibility changes.

For example:

Initial finding:

```markdown
## Observation

Missing request timeout.

## Decision

Pending.

## Verification

Pending.
```

Artifact owner updates only:

```markdown
## Decision

Accepted.

Rationale:
A timeout will be added to all outbound requests.
```

Verifier updates only:

```markdown
## Verification

Verified.

Evidence:
- Commit: abc123
- Check: npm test
```

This minimizes overlapping edits.

---

# 10. Merge Conflict Design Rules

Implement the following design constraints.

## Rule 1: One finding per file

Never store multiple findings in one mutable document.

Good:

```text
findings/
├── F001.md
├── F002.md
└── F003.md
```

Avoid:

```text
R042.md

F001
F002
F003
```

---

## Rule 2: Creating a finding must not modify existing findings

Creating:

```text
F004.md
```

must not require updating:

```text
F001.md
F002.md
F003.md
```

---

## Rule 3: Avoid global indexes

Do not require updates to:

```text
.review/README.md
```

when:

* creating a review,
* creating a finding,
* closing a finding,
* verifying a finding.

The filesystem is the primary index.

---

## Rule 4: Minimize shared files

The only shared review-level file should normally be:

```text
review.md
```

Agents should rarely need to modify it.

---

# 11. Remove Review Rounds

Remove review rounds as a required protocol concept.

Do not require:

```text
Round 1
Round 2
Round 3
```

Do not require:

```text
current_round
```

Do not require agents to discover the current round.

Use Git history and review base references instead.

Example:

```markdown
Base: abc123
```

Git provides:

```text
who
when
what changed
history
diff
```

Do not duplicate this temporal information unless required for review semantics.

---

# 12. Feedback Simplification

Remove feedback threads as a required RaC protocol primitive.

Do not require:

```text
feedback/
FB001
FB002
Feedback kinds
Feedback state
```

Discussion MAY happen:

* in pull requests,
* in issues,
* in external collaboration tools,
* in comments associated with findings.

RaC SHOULD record the final decision and verification result, not reproduce the entire conversation.

---

# 13. Markdown as the Canonical Source

Markdown MUST be the canonical source format.

Do not require agents to maintain both Markdown and JSON.

Target model:

```text
Markdown
    │
    ├── Human readable
    │
    └── Optional parser
             │
             ▼
            JSON
```

If machine-readable JSON is required:

```text
Markdown → parser → JSON
```

Do not use:

```text
Markdown + manually synchronized JSON
```

---

# 14. Simplify the JSON Schema

Update the schema to represent the simplified model.

Avoid:

```text
current_round
complex transition states
feedback workflow
state transition validation
```

The schema should validate basic structure only.

Example conceptual schema:

```json
{
  "id": "F001",
  "title": "Missing request timeout",
  "status": "open",
  "severity": "medium",
  "location": "src/payment/client.ts",
  "decision": "pending",
  "verification": "pending"
}
```

Do not encode workflow transitions in JSON Schema.

---

# 15. Simplify SKILL.md

Reduce `SKILL.md` substantially.

Target approximately 40–80 lines.

The skill should focus on:

1. discovery,
2. roles,
3. operations,
4. progressive loading.

## Suggested SKILL.md structure

```markdown
# Review as Code

## Discovery

1. Read repository instructions.
2. Locate `.review/`.
3. Locate the target review.
4. Load only artifacts required for the requested operation.

Do not load unrelated reviews or findings unless necessary.

## Roles

Reviewer:
- creates observations and findings.

Artifact Owner:
- owns decisions.

Implementer:
- changes the reviewed artifact.

Verifier:
- independently verifies results.

## Findings

- One finding per file.
- Finding IDs are stable.
- Findings are independently addressable.
- Observations must not be silently rewritten.
- Decisions are explicit.
- Verification records evidence.

## Operations

Review:
Create finding files.

Implement:
Modify the reviewed artifacts.

Decide:
Update the decision section.

Verify:
Update the verification section.

Close:
Close findings that have reached a final disposition.

## Outstanding Findings

A finding is outstanding when Status is open.

A review has no outstanding findings when all findings are closed.
```

Do not duplicate the entire specification inside the skill.

---

# 16. Progressive Agent Loading

Add an explicit cost optimization rule.

> Agents MUST load the minimum information necessary for the requested operation.

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

Avoid:

```text
Entire specification
Entire skill
All reviews
All findings
All history
Entire repository
```

unless the task actually requires them.

---

# 17. Agent Operations

Define operations with minimal write scope.

## Review operation

Input:

```text
Source files
Review scope
Relevant instructions
```

Output:

```text
New finding files
```

Example:

```text
Create:
.review/reviews/R042/findings/F004.md
```

Do not modify unrelated findings.

---

## Decision operation

Input:

```text
Target finding
Artifact owner response
```

Output:

```text
Update Decision section only
```

---

## Implementation operation

Input:

```text
Target finding
Relevant source files
```

Output:

```text
Source changes
```

Do not modify the finding unless explicitly authorized to record implementation evidence.

---

## Verification operation

Input:

```text
Target finding
Implementation
Relevant tests
```

Output:

```text
Verification result
Evidence
```

Only modify the target finding.

---

## Closure operation

Input:

```text
All finding statuses
```

Output:

```text
Review status update if appropriate
```

Before closing a review:

1. Enumerate finding files.
2. Check each finding Status.
3. If any finding is open, do not close the review.
4. Otherwise, close the review.

---

# 18. Implementation Sequence

Perform the migration in this order.

## Phase 1 — Specification

Update `SPEC.md`.

Tasks:

1. Introduce one-finding-per-file structure.
2. Remove rounds as a required concept.
3. Remove the transition matrix.
4. Replace complex states with `open` and `closed`.
5. Define Decision separately.
6. Define Verification separately.
7. Define section ownership.
8. Define outstanding findings.
9. Add progressive loading guidance.
10. Add merge-conflict minimization principles.

Do not change unrelated concepts during this phase.

---

## Phase 2 — Directory Examples

Update all examples.

Convert:

```text
reviews/
└── R001.md
```

to:

```text
reviews/
└── R001/
    ├── review.md
    └── findings/
        └── F001.md
```

Create examples for:

1. Open finding.
2. Accepted and verified finding.
3. Rejected finding.
4. Accepted risk.
5. Review with no outstanding findings.

---

## Phase 3 — SKILL.md

Replace verbose procedural content with the minimal agent workflow.

Requirements:

* progressive loading,
* one finding per file,
* ownership boundaries,
* minimal operations,
* no round discovery,
* no transition table.

---

## Phase 4 — Schema

Simplify `review.schema.json`.

Requirements:

* validate stable IDs,
* validate `open | closed`,
* validate decision vocabulary,
* validate verification vocabulary.

Do not encode workflow transition rules.

---

## Phase 5 — Documentation

Update:

```text
README.md
AGENTS.md
SPEC.md
SKILL.md
examples/
schemas/
```

Ensure terminology is consistent everywhere.

---

# 19. Compatibility and Migration

If backward compatibility is required, support the old format temporarily.

Suggested approach:

```text
Legacy:
.review/reviews/R001.md

New:
.review/reviews/R001/review.md
.review/reviews/R001/findings/F001.md
```

Do not make agents maintain both formats.

Migration should be one-way:

```text
Old format
    ↓
Migration tool
    ↓
New format
```

The migration tool MAY:

1. Create a review directory.
2. Extract each finding into its own file.
3. Convert legacy status to:

   * Status
   * Decision
   * Verification.
4. Preserve original IDs.
5. Preserve historical text where possible.

---

# 20. Acceptance Criteria

The implementation is complete when all of the following are true.

## Structure

* [ ] Every finding can exist in an independent file.
* [ ] Creating a finding does not require modifying another finding.
* [ ] No global mutable finding index is required.

## Workflow

* [ ] Findings have only `open` or `closed` status.
* [ ] Decisions are separate from status.
* [ ] Verification is separate from status.
* [ ] No transition matrix exists.
* [ ] Rounds are not required.

## Ownership

* [ ] Reviewer owns observations.
* [ ] Artifact owner owns decisions.
* [ ] Verifier owns verification.
* [ ] Observations cannot be silently rewritten by another role.

## Review Completion

* [ ] Outstanding findings can be mechanically derived.
* [ ] A review with any open finding cannot be closed.
* [ ] A review with all findings closed has no outstanding findings.

## Agent Efficiency

* [ ] Agents can operate on a single finding without loading unrelated findings.
* [ ] SKILL.md is substantially smaller.
* [ ] Agents are instructed to load progressively.
* [ ] No duplicated Markdown/JSON maintenance is required.

## Merge Safety

* [ ] Multiple agents can create different findings concurrently.
* [ ] Multiple agents can work on different findings concurrently.
* [ ] Most review operations modify one file or create one file.
* [ ] Shared mutable files are minimized.

---

# 21. Final Design Principles

The implementation MUST follow these principles.

## Principle 1

**One finding = one independently writable artifact.**

## Principle 2

**Minimize shared mutable files.**

## Principle 3

**Git history is the timeline. Do not duplicate it unnecessarily.**

## Principle 4

**Status is minimal. Decisions and verification are separate concepts.**

## Principle 5

**Observations belong to reviewers. Decisions belong to artifact owners. Verification belongs to verifiers.**

## Principle 6

**Review completion is derived from findings.**

## Principle 7

**Agents load progressively and only read what they need.**

## Principle 8

**Markdown is canonical. Machine-readable representations are generated when necessary.**

---

# Expected Result

The final model should be understandable as:

```text
Review
│
└── Independent Findings
     │
     ├── Observation → Reviewer
     ├── Decision → Artifact Owner
     └── Verification → Verifier
```

With:

```text
Finding status:
open | closed
```

And:

```text
Review complete
=
no finding with status open
```

The implementation should avoid becoming a workflow engine.

The primary goal is a small, Git-native protocol that works efficiently for humans and multiple parallel coding agents.
