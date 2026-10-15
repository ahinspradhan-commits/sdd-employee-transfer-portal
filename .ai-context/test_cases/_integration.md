# Cross-feature Integration Test Cases

**Status:** Draft inventory only. Execute after slice specs pass Gate 1 and corresponding implementation tests have RED/GREEN evidence. No executable tests currently exist.

| Test ID | ACs / slices | Scenario | Expected | Gate / dependency |
|---|---|---|---|---|
| INT-TC-01 | SUB-AC-01..11, ORC-AC-01..03 | Submit a valid request with deterministic fixture routing policy | Request and exactly fixture-selected actions commit atomically; response follows SUB contract | Gate 1; A-001/A-003; fixture is not a production routing rule |
| INT-TC-02 | SUB-AC-10, ORC-AC-02 | Force an action insert failure after request insert | Entire transaction rolls back; no request or action remains | Gate 1; database integration test |
| INT-TC-03 | TRK-AC-01..03, TRK-AC-05..08 | Submit as employee A; read as A and B; list as A | A sees only own request; B receives indistinguishable 404; list is owner-scoped and newest-first | Gate 1; A-001/A-002 |
| INT-TC-04 | ACT-AC-01..03, ACT-AC-05..06, TRK-AC-08 | Record one permitted fixture outcome, then read request | One conditional update wins; action is no longer pending and TRK reflects committed state | Gate 1; ACT access/endpoint remains blocked until BD-019 and relevant access decisions |
| INT-TC-05 | SEC-09-AC-01 | Attempt state-changing request without valid CSRF token | Safe 403; database state unchanged | A-001 validated; see `_security.md` |
| INT-TC-06 | SUB/TRK/ACT security ACs | Unauthenticated calls and cross-employee reads | 401 for unauthenticated calls; non-owner request access denied without existence disclosure | Gate 1; test-first required |
