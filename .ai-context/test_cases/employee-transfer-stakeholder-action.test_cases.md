# Test Cases — employee-transfer-stakeholder-action

**Source:** `specs/employee-transfer-stakeholder-action.spec.md`. **Status:** inventory only; no executable tests or RED evidence exist. Reconcile after Gate 1 review.

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| ACT-TC-01 | ACT-AC-01 | Service + DB | Fixture responsible user records fixture outcome | Row: pending 0, outcome set, `outcome_by_user_id` = caller, `outcome_at` set |
| ACT-TC-02 | ACT-AC-02 | Service + DB | Unrelated user records outcome | Refused; row unchanged |
| ACT-TC-03 | ACT-AC-03 | Service + DB | Record twice | Second refused with conflict; first outcome retained |
| ACT-TC-04 | ACT-AC-04 | Service + DB | Requester named responsible by fixture | **Deferred until BD-017 confirmation** |
| ACT-TC-05 | ACT-AC-05 | Service + DB | Two DB connections issue the conditional update simultaneously | Exactly one affected row in total; one conflict |
| ACT-TC-06 | ACT-AC-06 | Service + DB | Payload `outcome_by_user_id` = other user | Stored recorder = session user |
| ACT-TC-07 | ACT-AC-01 → TRK-AC-08 | API | Record via service, then GET TRK-API-01 | Action shows `pending: false` |
