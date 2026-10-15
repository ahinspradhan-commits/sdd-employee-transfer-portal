# Tasks — Employee Transfer Stakeholder Orchestration (ORC)

**Status:** Draft / gated; no task is executable until Feature Specification Gate 1 and constitution approval.

| Task ID | Work | Acceptance Criteria | Dependencies / Gate | Status |
|---|---|---|---|---|
| employee-transfer-stakeholder-orchestration.T01 | Write tests for stakeholder-action record shape and atomic request/action persistence using an injected fixture policy | ORC-AC-01..03; ORC-TC-01..04 | After Feature Gate 1; RED must be captured before implementation | Pending |
| employee-transfer-stakeholder-orchestration.T02 | Implement only the approved record contract and transaction boundary to GREEN | ORC-AC-01..03 | SUB contract approved; routing rules stay fixture-based until BD-001..007 confirmed | Pending |
| employee-transfer-stakeholder-orchestration.T03 | Verify action schema and traceability after approved routing decisions | AC/TC created after BD confirmation | BD-001..008, BD-015, BD-019 as applicable | Blocked |

**Blocked:** production routing, sequencing, responsible-party assignment, outcome derivation and external integration.
