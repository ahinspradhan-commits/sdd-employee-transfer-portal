# Tasks — Employee Transfer Stakeholder Action (ACT)

**Status:** Draft / gated; no task is executable until Feature Specification Gate 1 and constitution approval.

| Task ID | Work | Acceptance Criteria | Dependencies / Gate | Status |
|---|---|---|---|---|
| employee-transfer-stakeholder-action.T01 | Write service-level tests for conditional outcome update, authorization invariant and concurrent submissions | ACT-AC-01..03, ACT-AC-05..06; ACT-TC-01..03, ACT-TC-05..07 | After Feature Gate 1; capture RED before implementation | Pending |
| employee-transfer-stakeholder-action.T02 | Implement only approved service-level outcome invariants to GREEN | Approved ACT ACs excluding blocked ACT-AC-04 | BD-019 must be resolved before public/stakeholder endpoint; access rules remain deny-by-default | Pending |
| employee-transfer-stakeholder-action.T03 | Add requester/stakeholder authorization behavior and endpoint contracts | AC/TC to be written after decisions | BD-001/004..006/017/019; ACT-TC-04 deferred | Blocked |

**Blocked:** stakeholder endpoint, responsibility rules, outcome values, downstream effects and audit requirements until decisions are confirmed.
