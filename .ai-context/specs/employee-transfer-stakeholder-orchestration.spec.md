# Spec: Employee Transfer Stakeholder Orchestration

## Spec ID
employee-transfer-stakeholder-orchestration

## Status
Draft

## Version
v1.0

## Governance

| Role | Name |
|---|---|
| Project Owner | Ahin Subhra Pradhan |
| Gate 1 Reviewer | Sourav Kumar Maity |
| Gate 2 Reviewer | Ahin Subhra Pradhan |

### Gate 1 Status

Not Requested

### Gate 2 Status

Not Requested

## Linked BRD
.ai-context/BRD.md#BRD-001 (FR10)

## Parent Feature
Employee Internal Transfer Request (One-Point Employee Portal) — see `.ai-context/specs/spec-slice-map.md`

## Slice Sequence
02

## Intent
When a Transfer Request is submitted, the system generates one pending stakeholder task per stakeholder group (Manager, HR, Payroll, IT, Facilities) against that request, using a flat, non-conditional, non-sequenced model — the working assumption adopted because Business Decisions B3 (conditional triggering) and B4 (approval sequencing) are not yet resolved.

## Business Context
BRD.md §5 (Proposed Journey, step 4) identifies "the portal orchestrates the downstream stakeholder actions" as "the least-specified and most consequential part of the requirement." This slice isolates that orchestration logic into its own spec specifically so it can be revised independently — with its own new minor version — once B3/B4 are resolved, without requiring changes to `employee-transfer-request-submission`'s or `employee-transfer-status-tracking`'s contracts.

## Entry Conditions
- A Transfer Request has just been created with Status = "Submitted" (output of `employee-transfer-request-submission`).

## Exit Conditions
- Exactly five pending Stakeholder Task records exist for the request — one per stakeholder group (Manager, HR, Payroll, IT, Facilities) — each with Status = "Pending".

## Builds On
- `employee-transfer-request-submission` (the request this orchestration runs against)
- .ai-context/BRD.md#BRD-001 §20, Assumption A3 (employee's *current* manager only)

## Related Specs
- `employee-transfer-status-tracking` (reads the tasks this spec creates)
- `employee-transfer-stakeholder-task-management` (reads and mutates the tasks this spec creates)

## Scope

### In Scope
- Creating 5 pending Stakeholder Task records at submission time.
- Resolving the Manager task's assignee using the "current manager only" assumption (A3, BRD.md).

### Explicitly Out of Scope
- Conditional/sequenced orchestration of Payroll/IT/Facilities (B3, B4) — deferred; all five tasks are created for every request regardless of what changed.
- Manager-selection logic beyond "current manager only" (B2).
- Real integration with external Payroll/IT/Facilities/HRMS systems (BRD.md §21, Assumption A2) — tasks are internal records, not external system calls.

## Process Flow
1. `employee-transfer-request-submission.API01` successfully creates a request.
2. Orchestration logic creates 5 pending Stakeholder Task records against that request (Manager, HR, Payroll, IT, Facilities), all simultaneously, with no ordering.
3. Each task's status starts at "Pending".

## Acceptance Criteria
1. employee-transfer-stakeholder-orchestration.AC1 — Given a request that has just been submitted, when the orchestration logic runs, then exactly five pending stakeholder tasks are created (Manager, HR, Payroll, IT, Facilities) and — when the request is viewed via `employee-transfer-status-tracking` — its status is "Submitted" and all five stakeholder groups appear in `pending_stakeholders`. *(Placeholder per the Working Assumption above — subject to change once B3/B4 are resolved; this AC and its test are expected to be revised, not the request-submission or status-tracking contracts.)*

## API Contract
Not Applicable — this spec does not expose or consume its own API contract. It is a system-internal reaction to `employee-transfer-request-submission.API01`, observed externally through `employee-transfer-status-tracking.API01`.

## State Changes
| Current State | Trigger | Next State |
|---|---|---|
| (no stakeholder tasks exist for the request) | Request submitted (employee-transfer-request-submission.AC1) | 5 tasks created, each Status = Pending |

## Data Requirements
- **Stakeholder Task**: request_id, stakeholder_role (Manager / HR / Payroll / IT / Facilities), status, assignee (for Manager: the employee's current manager, per A3). This entity is owned here; `employee-transfer-status-tracking` and `employee-transfer-stakeholder-task-management` reference it rather than redefining it.

## Integration Requirements
- None (internal, no external system integration in v1.0 — Assumption A2).

## Non-Functional Constraints
- Reference constitution.md: task creation happens within the same transactional boundary as request submission where practicable (no partial-orchestration state should be observable).
- No PII in logs at any level.

## Spec-Derived Unit Test Cases
| Test ID | Acceptance Criteria | Scenario | Expected |
|---|---|---|---|
| employee-transfer-stakeholder-orchestration.UT01 | AC1 | Fetch a freshly submitted request (via employee-transfer-status-tracking.API01) | status = "Submitted"; all 5 roles present in pending_stakeholders |

## Dependencies

### Upstream
- employee-transfer-request-submission

### Downstream
- employee-transfer-status-tracking (reads task state)
- employee-transfer-stakeholder-task-management (reads and mutates task state)

## Risks / Open Decisions
- B2 (which manager confirms) — v1.0 assumes current manager only (A3); not asserted as final.
- B3 (conditional triggering logic for Payroll/IT/Facilities) — not resolved; flat placeholder used.
- B4 (approval sequencing) — not resolved; no ordering enforced.

> OPEN DECISION:
> The source requirement does not state whether steps 5–7 of the As-Is process (Payroll/IT/Facilities updates, BRD.md §4) run sequentially, in parallel, or independently once digitised. This spec does not assume an answer — it uses the flat/parallel placeholder only because v1.0 must be testable, not because the question is considered resolved.

## Traceability

### BRD
- BRD-001, FR10

### Previous Spec
- employee-transfer-request-submission

### Next Spec
- employee-transfer-status-tracking, employee-transfer-stakeholder-task-management (parallel, both depend on this spec's output)

## Definition of Ready
- [ ] Intent is unambiguous
- [ ] Acceptance criteria are testable
- [ ] Dependencies identified
- [ ] API contract defined where applicable
- [ ] Out-of-scope items identified
- [ ] Constitution constraints identified
- [ ] Traceability complete
