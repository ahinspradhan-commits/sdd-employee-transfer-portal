# Workflow: Generate Tests (test-first)

## Preconditions
- The task's spec has a recorded Feature Specification Gate 1 approval.
- The task is not `Pending`/`Blocked` in `tasks/<slug>.tasks.md`.
- The test cases exist in `test_cases/<slug>.test_cases.md` (or `_integration.md` / `_security.md`).

## Steps
1. Pick one task (`<slug>.T##`) and the TC-IDs it covers.
2. Write PHPUnit tests under `tests/` named after the TC-ID (for example `testSub_TC_03_rejectsMissingTargetDepartment`). One behaviour per test.
3. State-transition and workflow tests use the local MySQL test database or a rollback-isolated transaction. No mocks for the database in those tests.
4. Security TCs (auth, CSRF, access filtering, SQL injection inputs) are written as real tests, not comments.
5. Run the tests before any implementation exists. Record the **RED** output (command, date, failing count, reason) in the task row.
6. Implement the minimum code to pass. Run again and record **GREEN**.
7. Update `traceability.md` with the test file and method for each TC.

## Rules
- Never weaken or delete a test to get GREEN; change the spec through review instead.
- Never write tests for Blocked behaviour.
- Do not invent fixture data that implies an unapproved business rule; label fixture-only routing as such.
