# Spec: Employee Transfer Status Tracking

## Spec ID
employee-transfer-status-tracking

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
.ai-context/BRD.md#BRD-001 (FR8, FR9)

## Parent Feature
Employee Internal Transfer Request (One-Point Employee Portal) — see `.ai-context/specs/spec-slice-map.md`

## Slice Sequence
03

## Intent
An authenticated employee can view a single Transfer Request's current status and detail — including which stakeholder groups still hold a pending action — and view a summary list of their own requests. Access is restricted to the owning employee and assigned stakeholders only.

## Business Context
BRD.md §5 (Proposed Journey, step 5) and the Business Problem statement (§2) both centre on the employee "always know[ing] what's happening and who it's waiting on" — this is the read path that delivers that transparency, replacing the informal follow-ups of the current manual process.

## Entry Conditions
- At least one Transfer Request exists (`employee-transfer-request-submission`) with its stakeholder tasks generated (`employee-transfer-stakeholder-orchestration`).

## Exit Conditions
- The caller receives accurate, access-scoped status information.
- No state is mutated by this spec — it is read-only.

## Builds On
- `employee-transfer-request-submission` (request fields)
- `employee-transfer-stakeholder-orchestration` (Stakeholder Task entity and initial pending set)

## Related Specs
- `employee-transfer-stakeholder-task-management` (its completions are what change this spec's `pending_stakeholders` output over time — a runtime, not build-order, dependency)

## Scope

### In Scope
- Viewing a single request's detail: fields, status, pending_stakeholders (API01).
- Listing the authenticated employee's own requests, summary view (API02).
- Enforcing 403 for a caller who is neither the owning employee nor an assigned stakeholder, on the detail view.

### Explicitly Out of Scope
- Notifications of any kind — email, SMS, in-portal alert (BRD.md §20, B9). This spec relies solely on the employee actively viewing status in-portal.
- Any mutation of request or task state (owned by `employee-transfer-stakeholder-task-management`).

## Process Flow
1. Employee (or an assigned stakeholder, where applicable to a future slice) requests a request's detail or their own request list.
2. System resolves whether the caller is the owning employee or an assigned stakeholder for that specific request.
3. If authorized: system returns status, fields, and the current `pending_stakeholders` (stakeholder roles with a task still in "Pending").
4. If not authorized: system returns 403, no request data disclosed.

## Acceptance Criteria
1. employee-transfer-status-tracking.AC1 — Given a request with some stakeholder tasks already completed (via `employee-transfer-stakeholder-task-management`) and others not, when the employee views it, then `pending_stakeholders` lists only the stakeholder roles that have not yet completed their task.
2. employee-transfer-status-tracking.AC2 — Given a request where every stakeholder task has been marked complete, when the employee views it, then the request's overall status reflects full completion (exact label TBD — placeholder: "Completed").
3. employee-transfer-status-tracking.AC3 — Given a user who is neither the owning employee nor an assigned stakeholder for a request, when they attempt to view it, then the system returns 403 and no request data is disclosed. *(Local expression of CSR-002, spec-slice-map.md.)*

## API Contract

### employee-transfer-status-tracking.API01 — GET /api/transfer-requests/{id}

#### Request
No body.

#### Success Response (200)
```json
{ "request_id": "int", "department_id": "int", "location_id": "int", "role_id": "int", "effective_date": "date", "reason": "string|null", "status": "string", "pending_stakeholders": ["Manager", "HR", "Payroll", "IT", "Facilities"], "submitted_at": "datetime" }
```

#### Exceptions
| HTTP Code | Condition | Response |
|---|---|---|
| 401 | No authenticated session (CSR-001) | `{ "error": "unauthenticated" }` |
| 403 | Caller is neither the owning employee nor an assigned stakeholder for this request (CSR-002) | `{ "error": "forbidden" }` |
| 404 | Request does not exist | `{ "error": "not_found" }` |

### employee-transfer-status-tracking.API02 — GET /api/transfer-requests

#### Request
No body. Scope is implicitly the authenticated caller's own requests.

#### Success Response (200)
```json
{ "requests": [ { "request_id": "int", "status": "string", "submitted_at": "datetime" } ] }
```

#### Exceptions
| HTTP Code | Condition | Response |
|---|---|---|
| 401 | No authenticated session (CSR-001) | `{ "error": "unauthenticated" }` |

## State Changes
None — this spec is read-only.

## Data Requirements
- Reads the Transfer Request entity (owned by `employee-transfer-request-submission`) and the Stakeholder Task entity (owned by `employee-transfer-stakeholder-orchestration`). Defines no new entities.

## Integration Requirements
- None beyond the internal read of the two entities above.

## Non-Functional Constraints
- Reference constitution.md: each stakeholder/owner view must only return requests routed to that caller, enforced at the query/data-access layer, not only hidden in the UI (CSR-002).
- No PII in logs at any level.
- No formal SLA; working default of sub-1-second local page loads.

## Spec-Derived Unit Test Cases
| Test ID | Acceptance Criteria | Scenario | Expected |
|---|---|---|---|
| employee-transfer-status-tracking.UT01 | AC1 | One of five stakeholders completes their task (via employee-transfer-stakeholder-task-management), then fetch | pending_stakeholders has 4 remaining roles, excludes the completed one |
| employee-transfer-status-tracking.UT02 | AC2 | All 5 stakeholders complete their tasks (via employee-transfer-stakeholder-task-management), then fetch | overall status reflects completion |
| employee-transfer-status-tracking.UT03 | AC3 | An unrelated employee (not owner, not assigned stakeholder) fetches the request | 403, no request fields returned |

## Dependencies

### Upstream
- employee-transfer-request-submission
- employee-transfer-stakeholder-orchestration

### Downstream
- None (terminal read path; consumed continuously across the request's lifecycle)

## Risks / Open Decisions
- B9 (notification requirements) — not resolved; no notification mechanism exists, in-portal viewing is the only channel in v1.0.

## Traceability

### BRD
- BRD-001, FR8, FR9

### Previous Spec
- employee-transfer-stakeholder-orchestration

### Next Spec
- None — related to `employee-transfer-stakeholder-task-management` (runtime data dependency, not a build sequence)

## Definition of Ready
- [ ] Intent is unambiguous
- [ ] Acceptance criteria are testable
- [ ] Dependencies identified
- [ ] API contract defined where applicable
- [ ] Out-of-scope items identified
- [ ] Constitution constraints identified
- [ ] Traceability complete
