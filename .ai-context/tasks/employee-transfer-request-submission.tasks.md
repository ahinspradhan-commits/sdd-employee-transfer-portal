# Tasks — Employee Transfer Request Submission (SUB)

**Status:** Draft / gated; no task is executable until Feature Specification Gate 1 and constitution approval.

| Task ID | Work | Acceptance Criteria | Dependencies / Gate | Status |
|---|---|---|---|---|
| employee-transfer-request-submission.T01 | Create/verify PHPUnit and isolated MySQL test fixtures for SUB | SUB-AC-01..11; SEC-09-AC-01 where applicable | After Feature Gate 1, constitution approval and A-001/A-003 validation | Pending |
| employee-transfer-request-submission.T02 | Write SUB baseline tests and capture RED evidence before implementation | SUB-TC-01..16; SEC-09-TC-01 | Test-first; A-001 for auth/CSRF, A-003 for master data | Pending |
| employee-transfer-request-submission.T03 | Implement approved submission behavior to GREEN | Approved SUB ACs only | Only after T02 RED evidence and approved spec; no Blocked business behavior | Pending |
| employee-transfer-request-submission.T04 | Verify transaction/idempotency/security regressions and update traceability | SUB-AC-10/11, relevant security ACs | After implementation; no code currently exists | Pending |

**Blocked until decisions:** BD-003, BD-010, BD-011, BD-013, BD-016 as mapped by the spec. Do not implement proposed options as facts.
