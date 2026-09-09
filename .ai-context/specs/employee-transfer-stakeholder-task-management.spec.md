# Spec: Employee Transfer Stakeholder Task Management

## Spec ID
employee-transfer-stakeholder-task-management

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
.ai-context/BRD.md#BRD-001 (FR10, stakeholder side)

## Parent Feature
Employee Internal Transfer Request (One-Point Employee Portal) — see `.ai-context/specs/spec-slice-map.md`

## Slice Sequence
04

## Intent
An authenticated stakeholder (Manager, HR, Payroll, IT, or Facilities) can view their own pending tasks across transfer requests and mark an assigned task as complete. Only a positive "complete" action exists in v1.0 — there is no rejection path.

## Business Context
BRD.md §3 confirms Manager/HR/Payroll/IT/Facilities as the downstream actors who currently coordinate a transfer manually; this slice is their digital equivalent of "doing my part" of a transfer, one task at a time, replacing the off-system follow-ups described in BRD.md §2.

## Entry Conditions
- Pending stakeholder tasks exist for at least one request (output of `employee-transfer-stakeholder-orchestration`).

## Exit Conditions
- A targeted task's Status becomes "Completed".
- The request's `pending_stakeholders`, as read via `employee-transfer-status-tracking`, no longer includes that stakeholder role.

## Builds On
- `employee-transfer-stakeholder-orchestration` (the Stakeholder Task entity this spec reads and mutates)

## Related Specs
- `employee-transfer-status-tracking` (reflects the state changes this spec produces — a runtime, not build-order, dependency)

## Scope

### In Scope
- Listing the authenticated stakeholder's own pending tasks across requests (API01).
- Marking an assigned task as complete (API02).
- Enforcing 403 when the caller is not the assigned stakeholder for the targeted task.
- Enforcing 409 when the targeted task is already completed.

### Explicitly Out of Scope
- Rejection handling policy (BRD.md §20, B6) — no reject action exists; only a positive "complete" path.
- Any reassignment of a task to a different stakeholder or manager.

## Process Flow
1. Stakeholder requests their own pending task list.
2. Stakeholder selects a task assigned to them and calls the complete action, optionally with a comment.
3. System verifies the task is assigned to the caller and not already completed.
4. System sets the task's Status to "Completed".

## Acceptance Criteria
1. employee-transfer-stakeholder-task-management.AC1 — Given a stakeholder task assigned to the authenticated stakeholder for a request, when they call the complete action, then that task's status becomes "Completed" and it no longer appears in the request's `pending_stakeholders` (as observed via `employee-transfer-status-tracking`).
2. employee-transfer-stakeholder-task-management.AC2 — Given a stakeholder task that is not assigned to the authenticated caller, when they attempt to complete it, then the system returns 403 and the task's state is unchanged. *(Local expression of CSR-002, spec-slice-map.md.)*
3. employee-transfer-stakeholder-task-management.AC3 — Given a stakeholder task that has already been marked complete, when the same or another caller attempts to complete it again, then the system returns 409 and the task's state is unchanged.

## API Contract

### employee-transfer-stakeholder-task-management.API01 — GET /api/stakeholder-tasks

#### Request
No body. Scope is implicitly the authenticated stakeholder's own pending tasks.

#### Success Response (200)
```json
{ "tasks": [ { "request_id": "int", "stakeholder_role": "string", "status": "string" } ] }
```
*Field set kept minimal deliberately — constitution.md's per-stakeholder access scoping and no-PII-in-logs rules apply; the exact fields a stakeholder needs beyond this are a Plan-stage detail.*

#### Exceptions
| HTTP Code | Condition | Response |
|---|---|---|
| 401 | No authenticated session (CSR-001) | `{ "error": "unauthenticated" }` |

### employee-transfer-stakeholder-task-management.API02 — POST /api/stakeholder-tasks/{id}/complete

#### Request
```json
{ "comment": "string (optional)" }
```

#### Success Response (200)
```json
{ "task_id": "int", "status": "Completed" }
```

#### Exceptions
| HTTP Code | Condition | Response |
|---|---|---|
| 401 | No authenticated session (CSR-001) | `{ "error": "unauthenticated" }` |
| 403 | Task is not assigned to the caller (CSR-002) | `{ "error": "forbidden" }` |
| 404 | Task does not exist | `{ "error": "not_found" }` |
| 409 | Task already completed | `{ "error": "already_completed" }` |

## State Changes
| Current State | Trigger | Next State |
|---|---|---|
| Pending (assigned to caller) | Caller calls complete | Completed |
| Completed | Any caller calls complete again | Completed (unchanged, 409 returned) |

## Data Requirements
- Reads and mutates the Stakeholder Task entity (owned by `employee-transfer-stakeholder-orchestration`). Defines no new entities.

## Integration Requirements
- None beyond the internal read/write of the Stakeholder Task entity.

## Non-Functional Constraints
- Reference constitution.md: per-stakeholder access scoping enforced at the query/data-access layer, not only hidden in the UI (CSR-002).
- No PII in logs at any level.
- All SQL access uses parameterized queries/prepared statements.

## Spec-Derived Unit Test Cases
| Test ID | Acceptance Criteria | Scenario | Expected |
|---|---|---|---|
| employee-transfer-stakeholder-task-management.UT01 | AC1 | Assigned stakeholder calls complete | 200, task status "Completed" |
| employee-transfer-stakeholder-task-management.UT02 | AC2 | Non-assigned stakeholder calls complete on someone else's task | 403 |
| employee-transfer-stakeholder-task-management.UT03 | AC3 | Complete an already-completed task | 409, state unchanged |

> **Traceability note:** UT02 corresponds to the source spec's UT08, which was mismapped to the source spec's AC6 even though its scenario is an access-control case. It is correctly mapped here to AC2 — see `spec-slice-map.md`, "Correction made during slicing."

## Dependencies

### Upstream
- employee-transfer-stakeholder-orchestration

### Downstream
- employee-transfer-status-tracking (reflects this spec's state changes)

## Risks / Open Decisions
- B6 (rejection handling policy) — not resolved; no reject path exists in v1.0.

## Traceability

### BRD
- BRD-001, FR10 (stakeholder-facing side)

### Previous Spec
- employee-transfer-stakeholder-orchestration

### Next Spec
- None — related to `employee-transfer-status-tracking` (runtime data dependency, not a build sequence)

## Definition of Ready
- [ ] Intent is unambiguous
- [ ] Acceptance criteria are testable
- [ ] Dependencies identified
- [ ] API contract defined where applicable
- [ ] Out-of-scope items identified
- [ ] Constitution constraints identified
- [ ] Traceability complete
