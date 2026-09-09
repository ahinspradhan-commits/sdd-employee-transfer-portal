# Spec: Employee Transfer Request Submission

## Spec ID
employee-transfer-request-submission

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
.ai-context/BRD.md#BRD-001 (FR1–FR7)

## Parent Feature
Employee Internal Transfer Request (One-Point Employee Portal) — see `.ai-context/specs/spec-slice-map.md`

## Slice Sequence
01

## Intent
An authenticated employee can submit a new Internal Transfer Request from the One-Point Employee Portal — capturing a proposed department/business unit, location, and role/position, an effective date, and an optional free-text reason — producing a stored request in "Submitted" status.

## Business Context
This is the entry point of the transfer journey (BRD.md §5, To-Be steps 1–3): the employee-initiated capture step that replaces the current fully manual, off-system coordination. Everything downstream (stakeholder task generation, status tracking, stakeholder actions) depends on a request having been captured here first.

## Entry Conditions
- The employee has an authenticated Portal session (existing Portal login, reused — see project_context.md).
- The employee has access to the transfer request submission form/endpoint.

## Exit Conditions
- A new Transfer Request record exists with Status = "Submitted".
- The request is available for stakeholder-task generation (`employee-transfer-stakeholder-orchestration`).

## Builds On
- .ai-context/project_context.md (existing Portal; PHP/MySQL; Employee/Department/Location/Role/Manager data assumed to already exist — Assumption A1, BRD.md)

## Related Specs
- `employee-transfer-stakeholder-orchestration` (next — consumes the request this spec creates)
- `employee-transfer-status-tracking` (reads the reason/fields this spec stores — see AC3)

## Scope

### In Scope
- Capturing and validating department_id, location_id, role_id, effective_date (required) and reason (optional).
- Persisting the request with Status = "Submitted".
- Returning request_id, status, and submitted_at on success.

### Explicitly Out of Scope
- HR eligibility validation (BRD.md §20, B1) — any authenticated employee's submission is accepted in v1.0.
- Enforcing a single active request per employee / concurrent-request policy (B7).
- Minimum lead-time validation on effective date (B8) — only field presence is checked, not its value relative to today.
- Employee-type eligibility restrictions (B10).
- Request cancellation/withdrawal (B5) — no cancel capability exists once submitted.

## Process Flow
1. Employee opens the Internal Transfer Request form in the Portal.
2. Employee provides department, location, role, effective date, and optionally a reason.
3. Employee submits.
4. System validates required fields are present.
5. System persists the request with Status = "Submitted" and returns request_id/status/submitted_at.

## Acceptance Criteria
1. employee-transfer-request-submission.AC1 — Given an authenticated employee, when they submit a request with a department, location, role, and effective date but no reason, then the request is created successfully with reason stored as null (reason is the only optional field).
2. employee-transfer-request-submission.AC2 — Given an authenticated employee, when they submit a request missing the department, location, role, or effective date, then the submission is rejected with a validation error naming the missing field(s).
3. employee-transfer-request-submission.AC3 — Given an authenticated employee submitting a request with a reason provided, when the request is later viewed, then the stored reason matches what was submitted. *(Verification of this AC exercises `employee-transfer-status-tracking`'s view endpoint — see Integration Requirements.)*

## API Contract

### employee-transfer-request-submission.API01 — POST /api/transfer-requests

#### Request
```json
{ "department_id": "int", "location_id": "int", "role_id": "int", "effective_date": "date (YYYY-MM-DD)", "reason": "string (optional)" }
```

#### Success Response (201)
```json
{ "request_id": "int", "status": "string", "submitted_at": "datetime" }
```

#### Exceptions
| HTTP Code | Condition | Response |
|---|---|---|
| 400 | Missing department_id, location_id, role_id, or effective_date | `{ "error": "validation_error", "fields": ["..."] }` |
| 401 | No authenticated session (CSR-001) | `{ "error": "unauthenticated" }` |

*Not covered here (open, per BRD.md §20): a 409/concurrent-request rejection depends on B7; a lead-time rejection depends on B8.*

## State Changes
| Current State | Trigger | Next State |
|---|---|---|
| (no request exists) | Employee submits with all required fields | Submitted |

## Data Requirements
- **Transfer Request**: requester (employee), department_id, location_id, role_id, effective_date, reason (nullable), status, submitted_at.

## Integration Requirements
- AC3's outcome (stored reason) is observed through `employee-transfer-status-tracking.API01` — this spec owns writing the reason; that spec owns reading it back. No duplicate read contract is defined here.

## Non-Functional Constraints
- Reference constitution.md: all SQL access uses parameterized queries/prepared statements.
- Every state-changing endpoint requires an authenticated session (CSR-001, spec-slice-map.md).
- No PII in logs at any level.

## Spec-Derived Unit Test Cases
| Test ID | Acceptance Criteria | Scenario | Expected |
|---|---|---|---|
| employee-transfer-request-submission.UT01 | AC1 | Submit with all required fields, no reason | 201, request created, reason stored as null |
| employee-transfer-request-submission.UT02 | AC2 | Submit missing effective_date | 400, `fields` includes `effective_date` |
| employee-transfer-request-submission.UT03 | AC2 | Submit missing department_id | 400, `fields` includes `department_id` |
| employee-transfer-request-submission.UT04 | AC3 | Submit with a reason, then fetch detail (via employee-transfer-status-tracking.API01) | reason matches what was submitted |

## Dependencies

### Upstream
- None — this is the process entry point.

### Downstream
- `employee-transfer-stakeholder-orchestration` (consumes the created request)
- `employee-transfer-status-tracking` (reads the created request's fields)

## Risks / Open Decisions
- B1 (HR eligibility rules) — not resolved; no eligibility check exists in this slice.
- B7 (concurrent-request policy) — not resolved; not enforced.
- B8 (minimum lead time) — not resolved; not enforced.
- B10 (employee-type scope) — not resolved; not enforced.
- B5 (cancellation/withdrawal) — not resolved; out of scope for v1.0.

> OPEN DECISION:
> The source requirement does not state whether the proposed department/location/role may equal the employee's *current* values (BRD.md §15). Not enforced or rejected in this slice — carried forward as open, not assumed.

## Traceability

### BRD
- BRD-001, FR1–FR7

### Previous Spec
- None (entry point)

### Next Spec
- employee-transfer-stakeholder-orchestration

## Definition of Ready
- [ ] Intent is unambiguous
- [ ] Acceptance criteria are testable
- [ ] Dependencies identified
- [ ] API contract defined where applicable
- [ ] Out-of-scope items identified
- [ ] Constitution constraints identified
- [ ] Traceability complete
