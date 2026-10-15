# Test Cases — employee-transfer-status-tracking

**Source:** `specs/employee-transfer-status-tracking.spec.md`. **Status:** inventory only; no executable tests or RED evidence exist. Reconcile after Gate 1 review.

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
