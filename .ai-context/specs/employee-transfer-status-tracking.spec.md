# Spec: Employee Transfer Status Tracking

## Spec ID
employee-transfer-status-tracking (short code **TRK**)

## Status
Draft, ready for Gate 1 specification review

## Version
v2.0 (2026-09-28): re-baselined against BRD-001 v2.1. Supersedes v1.0.

## Governance

| Role | Name |
|---|---|
| Project Owner | Ahin Subhra Pradhan |
| Gate 1 Reviewer | Sourav Kumar Maity |
| Gate 2 Reviewer | TBD (independent of implementer) |

### Gate 1 Status
Requested (specification review pending)

### Gate 2 Status
Not Requested

## Linked BRD
`.ai-context/BRD.md#BRD-001` v2.1: BR-008, BR-009, BR-011, RULE-004, RULE-005, BAR-002, BAR-003, BAR-004, §6 Stage 5–6, §13, §14

## Parent Feature
See `spec-slice-map.md` v2.0. Slice sequence **03**.

---

## 1. Intent
An authenticated employee can see, in one view, the current status of each of their transfer requests and which stakeholder actions are still pending and with which stakeholder group. This read path delivers the "single view of progress" (BR-011). It never changes state.

## 2. Scope

### In scope (Baseline)
- Detail of one of the employee's own requests: submitted values, status, all stakeholder actions with pending flag.
- A list of the employee's own requests.
- Access scoping for the requester; additional stakeholder visibility remains blocked until BD-017 confirms who may view.

### Blocked
- The status value set and its transitions (BD-015, BD-007, BD-008, BD-009).
- How completion is presented and confirmed to the employee (BD-015, BD-012).
- Stakeholder visibility of requests they are involved in (BD-017, BAR-004).

### Reserved
- Notifications (BD-012).

### Out of scope
- Reporting/analytics (BRD §18).

## 3. Entry / Exit Conditions

| | Condition | Tag |
|---|---|---|
| Entry | Authenticated session | Baseline |
| Exit | Caller receives only data they are permitted to see; no state changed | Baseline |

## 4. Spec Requirements

| ID | Requirement | Trace | Tag |
|---|---|---|---|
| TRK-SR-01 | The requesting employee can view one of their requests: request ID, proposed department/location/role (ID and display name), effective date, reason, current status, and submitted timestamp. | BR-008, RULE-004 | Baseline for the request/read contract; **status value is Blocked (BD-015)** and display names depend on A-003 |
| TRK-SR-02 | The same view lists every stakeholder action on the request with its stakeholder group and whether it is pending. The employee can therefore tell which actions are pending and with whom. | BR-009, RULE-005 | Baseline |
| TRK-SR-03a | The requesting employee may view their own request. A caller without a confirmed access rule receives the same response as for a non-existent request. | BAR-002, SEC-03..05 | Baseline (deny-by-default); stakeholder access is separately blocked by TRK-SR-03b |
| TRK-SR-03b | Stakeholders may view requests they are involved in. | BAR-004, BD-017 | **Blocked (BD-017, BD-001, BD-004..006)**. Denied by default until confirmed |
| TRK-SR-04 | Status value set and request-level transitions. | §13, BD-015, BD-007..009 | **Blocked (BD-015 + BD-007..009)** |
| TRK-SR-05 | Completion is shown to the employee as confirmation that the transfer journey has finished. | §6 Stage 6, BD-015, BD-012 | **Blocked (BD-015)** for the definition; the channel beyond in-portal is Reserved (BD-012) |
| TRK-SR-06 | Notifications at business events. | §14, BD-012 | **Reserved (BD-012)** |
| TRK-SR-07 | The employee can list all their own requests, newest first, each showing request ID, the employee-visible status when BD-015 is confirmed, submitted timestamp and the number of pending actions. | BR-011 | Baseline for the list contract; **status value Blocked (BD-015)** |
| TRK-SR-08 | Both views reflect the committed state at read time. Once an ACT outcome is committed, it shows on the next read. | BR-011 | Baseline |

## 5. State Model — Request (feature-level)

The request-level lifecycle **is not defined** until BD-015 and BD-007..009 are confirmed. The only confirmed facts are:

| From | Trigger | To | Tag |
|---|---|---|---|
| (none) | Submission (SUB) | Request exists, with one or more pending stakeholder actions | Baseline |
| In progress | All required actions have an outcome | "Completed" or equivalent | **Blocked (BD-015, BD-007)** |
| In progress | A stakeholder records a negative outcome | "Rejected" or equivalent | **Blocked (BD-008, BD-015)** |
| In progress | Employee withdraws | "Cancelled" or equivalent | **Reserved (BD-009)** |

Candidate labels in BRD §13 (Draft, Submitted, Manager Pending, …) are **not** used. The API returns `status` as an opaque string whose value set is fixed at BD-015 confirmation.

## 6. API Contract

### TRK-API-01 — GET /api/transfer-requests/{request_id}

**200 OK**
```json
{
  "request_id": 1001,
  "status": "<value Blocked — BD-015>",
  "submitted_at": "2026-09-28T10:15:00+05:30",
  "proposed": {
    "department": { "id": 12, "name": "Finance" },
    "location":   { "id": 3,  "name": "Kolkata" },
    "role":       { "id": 45, "name": "Senior Analyst" }
  },
  "effective_date": "2026-11-01",
  "reason": "Relocating closer to family",
  "stakeholder_actions": [
    { "action_id": 5001, "stakeholder_group": "Manager", "pending": false, "outcome": "<value Blocked — BD-008>", "outcome_at": "2026-09-29T09:00:00+05:30" },
    { "action_id": 5002, "stakeholder_group": "HR",      "pending": true,  "outcome": null, "outcome_at": null }
  ],
  "pending_stakeholder_groups": ["HR"]
}
```
- `pending_stakeholder_groups` is derived: the distinct groups that have at least one `pending: true` action.
- Stakeholder actions do **not** expose the name of the individual responsible. Whether they should is part of BD-017.

**Errors**

| HTTP | `error` | Condition | Tag |
|---|---|---|---|
| 401 | `unauthenticated` | No session | Baseline |
| 404 | `not_found` | Request does not exist **or** caller is not the requester | Baseline (SEC-05) |
| 400 | `validation_error` | `request_id` is not a positive integer | Baseline |

### TRK-API-02 — GET /api/transfer-requests

**200 OK**
```json
{
  "requests": [
    { "request_id": 1001, "status": "<value Blocked — BD-015>", "submitted_at": "2026-09-28T10:15:00+05:30", "pending_action_count": 1 }
  ]
}
```
Scope: the session employee's own requests only, newest first. An empty list returns `{ "requests": [] }` with 200. Pagination is a Plan concern.

**Errors:** 401 `unauthenticated`.

## 7. Data Requirements
Reads Transfer Request (SUB) and Stakeholder Action (ORC). Reads display names from master data (A-003). Defines no entities.

## 8. Security Requirements
SEC-01, SEC-03, SEC-04, SEC-05, SEC-07, SEC-08 apply. Slice-specific:
- The ownership filter is part of the SQL query (`WHERE requester_employee_id = :session_employee`). It is not a post-fetch check (SEC-04).
- A non-owner gets a 404 whose body is byte-identical to the non-existent-ID response (SEC-05).

## 9. Failure Scenarios

| ID | Scenario | Expected handling | Tag |
|---|---|---|---|
| TRK-FS-01 | Master data for a stored ID no longer resolves | Return the ID with `name: null`; do not fail the request | Depends on A-003 |
| TRK-FS-02 | Database unavailable | 500 `internal_error`; no internals disclosed | Baseline |
| TRK-FS-03 | An ACT outcome is committed during a read | Read returns a consistent snapshot, either before or after the outcome, never a mix | Baseline |

## 10. Acceptance Criteria

| ID | Criterion | Trace | Tag |
|---|---|---|---|
| TRK-AC-01 | **Given** an employee's own submitted request, **when** they GET it, **then** the response contains the submitted values, a non-empty `status`, `submitted_at`, and every stakeholder action with group and `pending` flag. | TRK-SR-01, -02 | Baseline |
| TRK-AC-02 | **Given** a request where some actions have an outcome and others do not, **when** the requester views it, **then** `pending_stakeholder_groups` lists only the groups with at least one pending action. | TRK-SR-02, -08 | Baseline |
| TRK-AC-03 | **Given** a request owned by employee A, **when** employee B (with no Baseline access rule) requests it, **then** the response is 404 `not_found`, identical to a non-existent ID, and no request data is returned. | TRK-SR-03a, SEC-05 | Baseline |
| TRK-AC-04 | **Given** a user who is a stakeholder on the request, **when** they request it via TRK-API-01, **then** the response is 404 until BD-017 is confirmed. | TRK-SR-03a (deny-by-default), TRK-SR-03b | Baseline (interim denial). Replaced when BD-017 is confirmed |
| TRK-AC-05 | **Given** employee A has requests and employee B has requests, **when** A calls TRK-API-02, **then** only A's requests are returned, newest first, each with the correct `pending_action_count`. | TRK-SR-07, SEC-04 | Baseline |
| TRK-AC-06 | **Given** an employee with no requests, **when** they call TRK-API-02, **then** the response is 200 with an empty list. | TRK-SR-07 | Baseline |
| TRK-AC-07 | **Given** no session, **when** either endpoint is called, **then** the response is 401. | SEC-01 | Baseline |
| TRK-AC-08 | **Given** an outcome has just been committed via ACT, **when** the requester next views the request, **then** that action shows `pending: false` and its group is removed from `pending_stakeholder_groups` if it has no other pending action. | TRK-SR-08 | Baseline |
| — | Status labels, completion confirmation, stakeholder view, notifications | TRK-SR-03b, -04..-06 | **Blocked**. AC to be written on BD confirmation |

## 11. Spec-Derived Test Cases

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| TRK-TC-01 | TRK-AC-01 | API | Submit (fixture policy {Manager, HR}); GET as requester | 200; request fields match; 2 actions, both pending; no specific status label/value asserted |
| TRK-TC-02 | TRK-AC-02 | API | Fixture: 3 actions; record outcome on one; GET | `pending_stakeholder_groups` has 2 groups; excludes the completed one |
| TRK-TC-03 | TRK-AC-02 | Unit | Two HR actions, one pending, one not | `HR` still listed as pending |
| TRK-TC-04 | TRK-AC-03 | API | Employee B, with no confirmed access rule, GETs A's request; separately GETs ID 999999 | Both 404; bodies identical |
| TRK-TC-05 | TRK-AC-03 | Unit | Repository query as B for A's ID | Returns no row (the filter is in SQL) |
| TRK-TC-06 | TRK-AC-04 | API | Stakeholder user GETs the request before BD-017 confirms stakeholder visibility | 404 |
| TRK-TC-07 | TRK-AC-05 | API | A has 2 requests, B has 1; A lists | 2 items, newest first; counts correct |
| TRK-TC-08 | TRK-AC-06 | API | Employee with none lists | 200 `{ "requests": [] }` |
| TRK-TC-09 | TRK-AC-07 | API | No session on API-01 and API-02 | 401 each |
| TRK-TC-10 | TRK-AC-08 | API | Record outcome via ACT-API-02, then GET | Action `pending: false`; group removed |
| TRK-TC-11 | TRK-AC-01 (SEC-10) | API | `GET /api/transfer-requests/abc` | 400 `validation_error` |

## 12. Dependencies
- **Upstream:** SUB (request), ORC (actions).
- **Runtime:** ACT (outcomes change what TRK returns).
- **Decisions:** BD-007..009, BD-012, BD-015, BD-017.

## 13. Traceability
BR-008/009/011 → TRK-SR-01, -02, -03a, -07, -08 → TRK-AC-01..08 → TRK-TC-01..11. See `.ai-context/traceability.md`.

## 14. Definition of Ready
- [x] Read contract, scoping and errors defined
- [x] Status value set explicitly Blocked; no candidate label used
- [x] Failure scenarios defined
- [ ] BD-015 confirmed. **Required before release** (employee must see a meaningful status)
- [ ] BD-017 confirmed. **Required before stakeholder visibility**
- [ ] Gate 1 specification approval
