# Spec: Employee Transfer Request Submission

## Spec ID
employee-transfer-request-submission (short code **SUB**)

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
`.ai-context/BRD.md#BRD-001` v2.1: BR-001..BR-007, RULE-001..003, BAR-001, §12

## Parent Feature
See `spec-slice-map.md` v2.0 (conventions, SEC-xx, error envelope). Slice sequence **01**.

---

## 1. Intent
An authenticated employee can start an Internal Transfer Request in the One-Point Employee Portal, provide the proposed department/business unit, location, role/job position and effective date, optionally give a reason, and submit it. Submission creates exactly one stored request owned by that employee. That request then enters stakeholder processing (ORC).

## 2. Scope

### In scope (Baseline)
- Initiation and capture of the transfer details.
- Data-format and reference validation of those details.
- Submission, creating a persisted request owned by the session employee.
- Protection against accidental duplicate submission.

### Blocked (not built until the decision is confirmed)
- Effective-date business rules (BD-011).
- Eligibility of the employee or employee type (BD-003, BD-013).
- Concurrent active-request policy (BD-010).
- Business validation of proposed values, e.g. same as current, inactive values, reason length limit (BD-016).
- The status **value** assigned on submission (BD-015).

### Reserved
- Cancellation/withdrawal (BD-009).

### Out of scope
- Editing a submitted request. Not requested by source; it would need its own business decision. The v1.0 assumption "view but not edit" was removed, so this is neither asserted nor built.
- Draft/save-for-later. Not requested by source.

## 3. Entry / Exit Conditions

| | Condition | Tag |
|---|---|---|
| Entry | Caller has an authenticated portal session (SEC-01) | Baseline; Depends on A-001 |
| Entry | Department, location and role master data are readable (A-003) | Depends on A-003 |
| Exit | Exactly one new request exists, owned by the session employee, holding the submitted values and a submission timestamp | Baseline |
| Exit | The request is handed to ORC in the same transaction (ORC-SR-06) | Baseline |

## 4. Process Flow (Baseline)
1. Employee opens "Internal Transfer Request" in the portal (BR-001).
2. Portal presents selectable department/business unit, location and role/job-position values from master data, plus an effective date and an optional reason (BR-002..006).
3. Employee submits (BR-007).
4. System authenticates the caller and resolves the requester from the session (SEC-01, SEC-02).
5. System validates format and references (SUB-SR-01..03). On failure → 400, nothing stored.
6. *(Blocked checks SUB-SR-04, -06, -07, -08 would run here once confirmed.)*
7. System stores the request and hands it to ORC in one transaction. Response 201.

## 5. Spec Requirements

| ID | Requirement | Trace | Tag |
|---|---|---|---|
| SUB-SR-01 | The employee can initiate a transfer request and must supply proposed department/business unit, proposed location, proposed role/job position and effective date. | BR-001..005, RULE-001, RULE-002 | Baseline. Mandatory status is derived from RULE-002; only the reason is designated optional (RULE-003) |
| SUB-SR-02 | The reason is optional. When omitted or blank it is stored as null; when provided it is stored exactly as submitted (after trimming leading and trailing whitespace). | BR-006, RULE-003 | Baseline |
| SUB-SR-03 | Department, location and role values must reference existing entries in the portal's master data. Effective date must be a valid calendar date in `YYYY-MM-DD` format. Reason, if present, must not exceed the technical storage limit `REASON_MAX_LENGTH`, set in the Plan. | §12 (confirmed capture), SEC-10 | Baseline (technical integrity); master-data reference Depends on A-003 |
| SUB-SR-04 | Business validation of proposed values (equal to current, inactive values, business length limit on reason). | §12, BD-016 | **Blocked (BD-016)** |
| SUB-SR-05 | Submitting a valid request creates one persisted request owned by the session employee, records the submission timestamp, and returns the request identifier and current status. | BR-007, BR-008 | Baseline, **except** the status value, which is Blocked (BD-015) |
| SUB-SR-06 | Effective-date business restrictions (past dates, lead time). | §12, BD-011 | **Blocked (BD-011)**. Until confirmed, any valid calendar date is accepted in development builds only; see §11 Note |
| SUB-SR-07 | Employee / employee-type eligibility to submit. | BD-003, BD-013 | **Blocked (BD-003, BD-013)** |
| SUB-SR-08 | Policy on multiple active requests per employee. | BD-010 | **Blocked (BD-010)** |
| SUB-SR-09 | Employee cancellation/withdrawal. | BD-009 | **Reserved (BD-009)** |
| SUB-SR-10 | A resubmission carrying the same `Idempotency-Key` as a prior successful submission by the same employee returns the original result and creates no second request. The same key with a different payload returns 409 `idempotency_conflict`. | Failure handling (review finding 7); SEC-10 | Baseline (technical). Distinct from BD-010 concurrency policy |

## 6. Validation Rules (Baseline only)

| Field | Required | Rule | Error `fields[].code` |
|---|---|---|---|
| `department_id` | Yes | integer; exists in department master | `required`, `invalid_type`, `unknown_reference` |
| `location_id` | Yes | integer; exists in location master | `required`, `invalid_type`, `unknown_reference` |
| `role_id` | Yes | integer; exists in role master | `required`, `invalid_type`, `unknown_reference` |
| `effective_date` | Yes | valid `YYYY-MM-DD` calendar date | `required`, `invalid_format` |
| `reason` | No | string; length ≤ `REASON_MAX_LENGTH` | `invalid_type`, `too_long` |

All field errors are returned together in a single 400 response.

## 7. API Contract

### SUB-API-01 — POST /api/transfer-requests

**Headers:** portal session (SEC-01); CSRF token per portal convention (SEC-09); `Idempotency-Key: <opaque string, 1–64 chars>` (optional; recommended by the UI; SUB-SR-10)

**Request**
```json
{
  "department_id": 12,
  "location_id": 3,
  "role_id": 45,
  "effective_date": "2026-11-01",
  "reason": "Relocating closer to family"
}
```

**201 Created**
```json
{
  "request_id": 1001,
  "status": "<value Blocked — BD-015>",
  "submitted_at": "2026-09-28T10:15:00+05:30"
}
```
The `status` field is part of the contract. Its value set is defined only when BD-015 is confirmed. Tests assert that it is present and non-empty, never a specific label.

**Errors**

| HTTP | `error` | Condition | Side effects | Tag |
|---|---|---|---|---|
| 400 | `validation_error` | Any rule in §6 fails | None | Baseline |
| 401 | `unauthenticated` | No session | None | Baseline |
| 403 | `csrf_invalid` | CSRF check fails | None | Depends on A-001 |
| 409 | `idempotency_conflict` | Key reused with different payload | None | Baseline |
| 500 | `internal_error` | Persistence/transaction failure | None (rolled back) | Baseline |
| 409 | `active_request_exists` | — | — | Reserved (BD-010) |
| 422 | `not_eligible` | — | — | Reserved (BD-003, BD-013) |
| 422 | `effective_date_not_allowed` | — | — | Reserved (BD-011) |
| 422 | `proposed_value_not_allowed` | — | — | Reserved (BD-016) |

## 8. State Changes

| From | Trigger | To | Tag |
|---|---|---|---|
| (none) | Valid submission | Request exists; enters stakeholder processing (ORC) | Baseline |
| (none) | Invalid submission | No request | Baseline |
| Request exists | Replay with same Idempotency-Key and same payload | Unchanged; original 201 body returned | Baseline |
| Any | Employee cancels | — | Reserved (BD-009) |

The initial status **label** is Blocked (BD-015). See TRK §State Model.

## 9. Data Requirements
**Transfer Request** (owned by this spec): `request_id`, `requester_employee_id` (from session), `department_id`, `location_id`, `role_id`, `effective_date`, `reason` (nullable), `status` (value set Blocked, BD-015), `submitted_at`, `idempotency_key` (nullable; unique per requester).
Physical schema, column types and indexes are defined in the Plan (TD-005).

## 10. Security Requirements
SEC-01, SEC-02, SEC-03, SEC-07, SEC-08, SEC-09, SEC-10 apply (see `spec-slice-map.md` §4). Slice-specific expressions:
- The requester cannot submit on behalf of another employee: a payload `employee_id` is ignored (SEC-02 → BAR-001).
- The `reason` value is free text and is never written to logs (SEC-08).

## 11. Failure Scenarios

| ID | Scenario | Expected handling | Tag |
|---|---|---|---|
| SUB-FS-01 | Double-click / network retry with the same Idempotency-Key | One request; both calls return the same 201 body | Baseline |
| SUB-FS-02 | Database failure during insert or ORC hand-off | Transaction rolled back; 500 `internal_error`; no partial request or actions persisted | Baseline |
| SUB-FS-03 | Master-data source unavailable | 500 `internal_error`; nothing stored; the error is logged without PII | Depends on A-003 |
| SUB-FS-04 | Session expires between form load and submit | 401; nothing stored | Baseline |
| SUB-FS-05 | Master-data value deleted between form load and submit | 400 `unknown_reference` on that field | Depends on A-003 |

> **Note on SUB-SR-06:** accepting any valid calendar date is a development-only placeholder so that Baseline tests can run. It is not a business rule and must not ship. Release is gated on BD-011 (see `traceability.md` release blockers).

## 12. Acceptance Criteria

| ID | Criterion | Trace | Tag |
|---|---|---|---|
| SUB-AC-01 | **Given** an authenticated employee, **when** they submit valid department, location, role and effective date with no reason, **then** a request is created with those values and a null reason, owned by that employee, and the response is 201 with `request_id`, a non-empty `status` and `submitted_at`. | SUB-SR-01, -02, -05 | Baseline |
| SUB-AC-02 | **Given** an authenticated employee, **when** they submit with a reason, **then** the stored reason equals the submitted reason (trimmed) and is returned by TRK-API-01. | SUB-SR-02 | Baseline |
| SUB-AC-03 | **Given** an authenticated employee, **when** any of department, location, role or effective date is missing, **then** the response is 400 `validation_error` listing **every** missing field with code `required`, and no request is stored. | SUB-SR-01 | Baseline |
| SUB-AC-04 | **Given** an authenticated employee, **when** a field has the wrong type or format (non-integer ID, invalid date such as `2026-02-30`, reason over `REASON_MAX_LENGTH`), **then** the response is 400 with the matching field code and nothing is stored. | SUB-SR-03 | Baseline |
| SUB-AC-05 | **Given** an authenticated employee, **when** a department, location or role ID does not exist in master data, **then** the response is 400 with `unknown_reference` on that field and nothing is stored. | SUB-SR-03 | Depends on A-003 |
| SUB-AC-06 | **Given** no authenticated session, **when** a submission is made, **then** the response is 401 and nothing is stored. | SEC-01 | Baseline |
| SUB-AC-07 | **Given** an authenticated employee, **when** the payload contains another employee's ID, **then** the request is owned by the session employee. | SEC-02, BAR-001 | Baseline |
| SUB-AC-08 | **Given** a successful submission with an Idempotency-Key, **when** the same employee repeats it with the same key and payload, **then** the original 201 body is returned and exactly one request exists. | SUB-SR-10 | Baseline |
| SUB-AC-09 | **Given** a successful submission with an Idempotency-Key, **when** the same key is reused with a different payload, **then** the response is 409 `idempotency_conflict` and no second request exists. | SUB-SR-10 | Baseline |
| SUB-AC-10 | **Given** a failure while persisting the request or its ORC hand-off, **when** submission is attempted, **then** the response is 500 `internal_error`, neither the request nor any stakeholder action exists, and the body contains no internal details. | SUB-FS-02, ORC-SR-06, SEC-08 | Baseline |
| SUB-AC-11 | **Given** a valid submission containing a reason, **when** application logs are inspected, **then** neither the reason nor employee PII appears. | SEC-08 | Baseline |
| — | Effective-date rules, eligibility, concurrency, business validation, initial status label, cancellation | SUB-SR-04, -06..-09 | **Blocked**. AC to be written on BD confirmation |

## 13. Spec-Derived Test Cases

Level: **API** = HTTP-level test against a real local MySQL test DB with rollback isolation (Constitution §2); **Unit** = validator/service unit test.

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| SUB-TC-01 | SUB-AC-01 | API | All required fields valid, no reason | 201; row exists with values; reason null; owner = session employee; `status` non-empty |
| SUB-TC-02 | SUB-AC-01 | Unit | Reason is `"   "` (whitespace only) | Stored as null |
| SUB-TC-03 | SUB-AC-02 | API | Submit with reason, then GET TRK-API-01 | Returned reason equals trimmed input |
| SUB-TC-04 | SUB-AC-03 | Unit | Each required field omitted individually (4 cases) | 400; `fields` contains that field with `required` |
| SUB-TC-05 | SUB-AC-03 | API | All four required fields omitted | 400; `fields` lists all four; row count unchanged |
| SUB-TC-06 | SUB-AC-04 | Unit | `department_id: "abc"` | 400; `invalid_type` |
| SUB-TC-07 | SUB-AC-04 | Unit | `effective_date: "2026-02-30"` and `"01/11/2026"` | 400; `invalid_format` |
| SUB-TC-08 | SUB-AC-04 | Unit | Reason length `REASON_MAX_LENGTH` and `REASON_MAX_LENGTH + 1` | First accepted; second 400 `too_long` |
| SUB-TC-09 | SUB-AC-05 | API | `location_id` not in master | 400; `unknown_reference` on `location_id`; nothing stored |
| SUB-TC-10 | SUB-AC-06 | API | No session cookie | 401; row count unchanged |
| SUB-TC-11 | SUB-AC-07 | API | Payload includes `employee_id` of employee B, session = A | Request owner = A |
| SUB-TC-12 | SUB-AC-08 | API | Same key + payload sent twice | Both 201 with identical body; one row |
| SUB-TC-13 | SUB-AC-09 | API | Same key, different `role_id` | 409 `idempotency_conflict`; one row |
| SUB-TC-14 | SUB-AC-10 | API | Force failure in ORC hand-off (test double) | 500; zero request rows and zero action rows; body has no SQL/stack trace |
| SUB-TC-15 | SUB-AC-11 | API | Submit with distinctive reason; scan captured log output | Reason string and employee name absent |
| SUB-TC-16 | SUB-AC-01 (SEC-07) | Unit | Reason containing `'; DROP TABLE --` | Stored verbatim; no SQL error |

## 14. Dependencies
- **Upstream:** None (entry point). A-001 and A-003 must be validated.
- **Downstream:** ORC (hand-off in the same transaction); TRK (reads request fields).

## 15. Traceability
BR-001..007 → SUB-SR-01..05, -10 → SUB-AC-01..11 → SUB-TC-01..16. Full matrix: `.ai-context/traceability.md`.

## 16. Definition of Ready
- [x] Intent is unambiguous
- [x] Every AC is individually identifiable and testable
- [x] Baseline vs Blocked items are explicit; no Proposed BD option is used as a rule
- [x] API contract, errors and reserved codes defined
- [x] Failure scenarios defined
- [x] Security requirements mapped to BAR/Constitution
- [x] Traceability complete for Baseline items
- [ ] A-001 and A-003 validated (BRD §11.1)
- [ ] Gate 1 specification approval
