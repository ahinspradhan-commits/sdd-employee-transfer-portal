# Test Cases — employee-transfer-stakeholder-orchestration

**Source:** `specs/employee-transfer-stakeholder-orchestration.spec.md`. **Status:** inventory only; no executable tests or RED evidence exist. Reconcile after Gate 1 review.

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| ORC-TC-01 | ORC-AC-01 | API | Fixture policy returns {Manager, HR}; submit | 2 actions; groups match; all pending; outcome null |
| ORC-TC-02 | ORC-AC-01 | API | Fixture policy returns all five groups; submit | 5 actions, one per group. **This is a fixture, not a business rule** |
| ORC-TC-03 | ORC-AC-02 | API | Fixture forces an insert failure on the 2nd action | 500; zero request rows; zero action rows |
| ORC-TC-04 | ORC-AC-03 | Unit | Policy returns `"Legal"` | Exception; transaction rolled back |
