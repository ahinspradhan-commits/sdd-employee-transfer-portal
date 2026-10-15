# Test Cases — employee-transfer-request-submission

**Source:** `specs/employee-transfer-request-submission.spec.md`. **Status:** inventory only; no executable tests or RED evidence exist. Reconcile after Gate 1 review.

Level: **API** = HTTP-level test against a real local MySQL test DB with rollback isolation (Constitution §2); **Unit** = validator/service unit test.

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| SUB-TC-01 | SUB-AC-01 | API | All required fields valid, no reason | 201; row exists with values; reason null; owner = session employee; `request_id` and `submitted_at` present |
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
