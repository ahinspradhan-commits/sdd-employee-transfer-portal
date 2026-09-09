# Spec: Employee Internal Transfer Request

## Spec ID
employee-internal-transfer

## Status
Draft v1.0 — Ready for Gate 1 Peer Review

> **Superseded (2026-09-03):** This monolithic spec has been decomposed into 4 process slices for independent Gate 1 review — see `.ai-context/specs/spec-slice-map.md`. Retained here as historical/reference input, not deleted, per the constitution's archival convention. Do not review or implement against this file going forward; review the slices instead:
> - `employee-transfer-request-submission.spec.md`
> - `employee-transfer-stakeholder-orchestration.spec.md`
> - `employee-transfer-status-tracking.spec.md`
> - `employee-transfer-stakeholder-task-management.spec.md`

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
.ai-context/BRD.md#BRD-001

## Intent
An authenticated employee can submit an Internal Transfer Request from the One-Point Employee Portal — selecting a proposed department/business unit, location, and role/position, an effective date, and an optional reason — and can then track the request's status and see which stakeholder(s) (Manager, HR, Payroll, IT, Facilities) currently hold a pending action against it. This replaces the current fully manual, off-system coordination between the employee, their manager, HR, Payroll, IT, and Facilities.

## Context
- Builds on: .ai-context/project_context.md (existing One-Point Portal; PHP/MySQL; Employee/Department/Location/Role/Manager data assumed to already exist — Assumption A1)
- Related: .ai-context/BRD.md#BRD-001 (source requirement and full Discovery Analysis)
- Constitution: .ai-context/constitution.md (Draft v0.1, approved for this stage)

## Open Business Decisions (Pending Gate 1)
This spec deliberately does **not** resolve the following. They are business-level open items carried forward from Discovery (BRD.md §20, B1–B10) and must be resolved — by you, acting as the process owner, since there is no external stakeholder for this exercise (project_context.md) — before implementation tasks are generated for the behavior they affect. Nothing below is treated as decided; where a placeholder default was needed to make v1.0 testable at all, it is called out explicitly in the AC/API sections, never silently.

| ID | Decision | Affects in this spec |
|---|---|---|
| B1 | HR eligibility rules for a transfer | No eligibility check exists in v1.0 AC — any authenticated employee's submission is accepted |
| B2 | Which manager(s) confirm (current / new / both) | v1.0 assumes the employee's *current* manager only (Assumption A3) — not asserted as final |
| B3 | Conditional triggering logic for Payroll/IT/Facilities | v1.0 uses a flat placeholder: all five stakeholder groups are pending on every request (see AC4 note) |
| B4 | Approval sequencing (sequential vs parallel) | v1.0 uses the same flat, non-sequential placeholder — no ordering is enforced |
| B5 | Cancellation/withdrawal policy | Not implemented — no cancel capability in v1.0 |
| B6 | Rejection handling policy | Not implemented — only a positive "complete" action exists for stakeholders in v1.0; no reject path |
| B7 | Concurrent-request policy | Not enforced — v1.0 does not block multiple active requests per employee |
| B8 | Minimum lead time for effective date | Not enforced — v1.0 only checks the field is present, not its value relative to today |
| B9 | Notification requirements (channel/triggers) | Not implemented — v1.0 relies solely on the employee viewing status in-portal |
| B10 | Employee-type scope (all vs excluded categories) | Not enforced — v1.0 does not distinguish employee type |

**Working assumption used to make v1.0 testable (flagged, not final):** in the absence of B3/B4 resolution, every submitted request creates one pending task per stakeholder group (Manager, HR, Payroll, IT, Facilities) simultaneously, with no ordering or conditional skipping. This is the simplest model consistent with the confirmed requirement text and is expected to be replaced once B3/B4 are decided — at which point this spec gets a new minor version.

## API Contract

### employee-internal-transfer.API01 — POST /api/transfer-requests
Submit a new Internal Transfer Request.

**Request payload:**
```json
{ "department_id": "int", "location_id": "int", "role_id": "int", "effective_date": "date (YYYY-MM-DD)", "reason": "string (optional)" }
```
**Success response (201):**
```json
{ "request_id": "int", "status": "string", "submitted_at": "datetime" }
```
**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 400 | Missing department_id, location_id, role_id, or effective_date | `{ "error": "validation_error", "fields": ["..."] }` |
| 401 | No authenticated session | `{ "error": "unauthenticated" }` |

*Not covered here (open): a 409/concurrent-request rejection depends on B7; a lead-time rejection depends on B8.*

### employee-internal-transfer.API02 — GET /api/transfer-requests/{id}
View a single request's detail, status, and pending stakeholders.

**Success response (200):**
```json
{ "request_id": "int", "department_id": "int", "location_id": "int", "role_id": "int", "effective_date": "date", "reason": "string|null", "status": "string", "pending_stakeholders": ["Manager", "HR", "Payroll", "IT", "Facilities"], "submitted_at": "datetime" }
```
**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 404 | Request does not exist | `{ "error": "not_found" }` |
| 403 | Caller is neither the owning employee nor an assigned stakeholder for this request | `{ "error": "forbidden" }` |

### employee-internal-transfer.API03 — GET /api/transfer-requests
List the authenticated employee's own requests (summary view).

**Success response (200):**
```json
{ "requests": [ { "request_id": "int", "status": "string", "submitted_at": "datetime" } ] }
```

### employee-internal-transfer.API04 — GET /api/stakeholder-tasks
List pending tasks assigned to the authenticated stakeholder (Manager/HR/Payroll/IT/Facilities).

**Success response (200):**
```json
{ "tasks": [ { "request_id": "int", "stakeholder_role": "string", "status": "string" } ] }
```
*Field set kept minimal deliberately — constitution.md's per-stakeholder access scoping and no-PII-in-logs rules apply; exact fields a stakeholder needs are a Plan-stage detail.*

### employee-internal-transfer.API05 — POST /api/stakeholder-tasks/{id}/complete
The authenticated stakeholder marks their own pending task as done (positive path only — no reject action defined, per B6 being open).

**Request payload:**
```json
{ "comment": "string (optional)" }
```
**Success response (200):**
```json
{ "task_id": "int", "status": "Completed" }
```
**Exceptions:**
| Code | Condition | Response body |
|---|---|---|
| 403 | Task is not assigned to the caller | `{ "error": "forbidden" }` |
| 404 | Task does not exist | `{ "error": "not_found" }` |
| 409 | Task already completed | `{ "error": "already_completed" }` |

## Acceptance Criteria
1. **employee-internal-transfer.AC1** — Given an authenticated employee, when they submit a request with a department, location, role, and effective date but no reason, then the request is created successfully (reason is the only optional field).
2. **employee-internal-transfer.AC2** — Given an authenticated employee, when they submit a request missing the department, location, role, or effective date, then the submission is rejected with a validation error naming the missing field(s).
3. **employee-internal-transfer.AC3** — Given an authenticated employee submitting a request with a reason provided, when the request is later viewed, then the stored reason is returned.
4. **employee-internal-transfer.AC4** — Given a request that has just been submitted, when the employee views it, then its status is "Submitted" and all five stakeholder groups (Manager, HR, Payroll, IT, Facilities) appear in `pending_stakeholders`. *(Placeholder per the Working Assumption above — subject to change once B3/B4 are resolved.)*
5. **employee-internal-transfer.AC5** — Given a request with some stakeholder tasks already completed and others not, when the employee views it, then `pending_stakeholders` lists only the stakeholder roles that have not yet completed their task.
6. **employee-internal-transfer.AC6** — Given a stakeholder task assigned to the authenticated stakeholder for a request, when they call the complete action, then that task's status becomes "Completed" and it no longer appears in the request's `pending_stakeholders`.
7. **employee-internal-transfer.AC7** — Given a request where every stakeholder task has been marked complete, when the employee views it, then the request's overall status reflects full completion (exact label TBD — placeholder: "Completed").
8. **employee-internal-transfer.AC8** — Given a user who is neither the owning employee nor an assigned stakeholder for a request, when they attempt to view or act on it, then the system returns 403 and no request data is disclosed.
9. **employee-internal-transfer.AC9** — Given a stakeholder task that has already been marked complete, when the same or another caller attempts to complete it again, then the system returns 409 and the task's state is unchanged.

## Unit Test Cases (spec-derived)
| Test ID | Maps to AC | Scenario | Expected |
|---|---|---|---|
| employee-internal-transfer.UT01 | AC1 | Submit with all required fields, no reason | 201, request created, reason stored as null |
| employee-internal-transfer.UT02 | AC2 | Submit missing effective_date | 400, `fields` includes `effective_date` |
| employee-internal-transfer.UT03 | AC2 | Submit missing department_id | 400, `fields` includes `department_id` |
| employee-internal-transfer.UT04 | AC3 | Submit with a reason, then fetch detail | reason matches what was submitted |
| employee-internal-transfer.UT05 | AC4 | Fetch a freshly submitted request | status = "Submitted"; all 5 roles in pending_stakeholders |
| employee-internal-transfer.UT06 | AC5 | One of five stakeholders completes their task, then fetch | pending_stakeholders has 4 remaining roles, excludes the completed one |
| employee-internal-transfer.UT07 | AC6 | Assigned stakeholder calls complete | 200, task status "Completed" |
| employee-internal-transfer.UT08 | AC6 | Non-assigned stakeholder calls complete on someone else's task | 403 |
| employee-internal-transfer.UT09 | AC7 | All 5 stakeholders complete their tasks, then fetch | overall status reflects completion |
| employee-internal-transfer.UT10 | AC8 | An unrelated employee (not owner, not assigned stakeholder) fetches the request | 403, no request fields returned |
| employee-internal-transfer.UT11 | AC9 | Complete an already-completed task | 409, state unchanged |

## Explicitly Out of Scope
- HR eligibility validation (B1)
- Manager-selection logic beyond "current manager only" (B2)
- Conditional/sequenced orchestration of Payroll/IT/Facilities (B3, B4)
- Request cancellation/withdrawal (B5)
- Rejection of a request or of a stakeholder task (B6)
- Enforcing a single active request per employee (B7)
- Minimum lead-time validation on effective date (B8)
- Notifications of any kind — email, SMS, in-portal alert (B9)
- Employee-type eligibility restrictions (B10)
- Real integration with external Payroll/IT/Facilities/HRMS systems (BRD §21, Assumption A2)
- Reporting/analytics dashboards, mobile app, master-data management UI (BRD §21)

## Non-Functional Constraints (from constitution.md — Draft v0.1, placeholders)
- 70% line coverage floor for this module (adjustable placeholder)
- No public/external API surface by default; all endpoints above are internal to the Portal
- No formal SLA; working default of sub-1-second local page loads
- No PII in logs at any level; per-stakeholder access scoping enforced at the query layer (AC8 above)
