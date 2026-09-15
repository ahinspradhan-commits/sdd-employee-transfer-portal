# Project Status Board
_Last updated: 2026-09-15_

## Active Specs
| Spec ID | Title | Status | Project Owner | Gate 1 Reviewer | Gate 1 Status | Gate 2 Reviewer | Gate 2 Status | Last Updated | Notes |
|---|---|---|---|---|---|---|---|---|---|
| employee-transfer-request-submission | Employee Transfer Request Submission | Draft | Ahin Subhra Pradhan | Sourav Kumar Maity | Rework Required | Ahin Subhra Pradhan | Not Requested | 2026-09-15 | Slice 01 of 4 — see spec-slice-map.md. Gate 1 rework required, see reviews/GATE1-BRD-001-2026-09-15.md |
| employee-transfer-stakeholder-orchestration | Employee Transfer Stakeholder Orchestration | Draft | Ahin Subhra Pradhan | Sourav Kumar Maity | Rework Required | Ahin Subhra Pradhan | Not Requested | 2026-09-15 | Slice 02 of 4 — B3/B4 still open, see Risks section. Gate 1 rework required, see reviews/GATE1-BRD-001-2026-09-15.md |
| employee-transfer-status-tracking | Employee Transfer Status Tracking | Draft | Ahin Subhra Pradhan | Sourav Kumar Maity | Rework Required | Ahin Subhra Pradhan | Not Requested | 2026-09-15 | Slice 03 of 4. Gate 1 rework required, see reviews/GATE1-BRD-001-2026-09-15.md |
| employee-transfer-stakeholder-task-management | Employee Transfer Stakeholder Task Management | Draft | Ahin Subhra Pradhan | Sourav Kumar Maity | Rework Required | Ahin Subhra Pradhan | Not Requested | 2026-09-15 | Slice 04 of 4. Gate 1 rework required, see reviews/GATE1-BRD-001-2026-09-15.md |

## Governance Issues

- GOVERNANCE ISSUE: employee-transfer-request-submission — Project Owner and Gate 2 Reviewer are the same person (Ahin Subhra Pradhan). If the Project Owner performs the implementation, an independent Gate 2 reviewer must be assigned before Gate 2 review begins.
- GOVERNANCE ISSUE: employee-transfer-stakeholder-orchestration — Project Owner and Gate 2 Reviewer are the same person (Ahin Subhra Pradhan). If the Project Owner performs the implementation, an independent Gate 2 reviewer must be assigned before Gate 2 review begins.
- GOVERNANCE ISSUE: employee-transfer-status-tracking — Project Owner and Gate 2 Reviewer are the same person (Ahin Subhra Pradhan). If the Project Owner performs the implementation, an independent Gate 2 reviewer must be assigned before Gate 2 review begins.
- GOVERNANCE ISSUE: employee-transfer-stakeholder-task-management — Project Owner and Gate 2 Reviewer are the same person (Ahin Subhra Pradhan). If the Project Owner performs the implementation, an independent Gate 2 reviewer must be assigned before Gate 2 review begins.

## Daily Execution Log

### 2026-09-03
- Decomposed the monolithic `employee-internal-transfer` spec (Draft v1.0, was Ready for Gate 1) into 4 sequential process slices per `spec-slice-map.md`. Original spec retained, marked superseded, not deleted. All 4 slices are Draft, pending individual Gate 1 review. No plans/tasks generated yet.

### 2026-09-09
- Added named ownership and review accountability model (Project Owner, Gate 1 Reviewer, Gate 2 Reviewer) across `project_context.md`, all active spec slices, and the spec/plan/tasks templates. No gates were approved automatically; all Gate 1/Gate 2 statuses remain "Not Requested". Flagged a governance issue: Project Owner and Gate 2 Reviewer are the same person on all 4 active slices — see Governance Issues above.

### 2026-09-15
- Gate 1 Reviewer (Sourav Kumar Maity) completed a manual Gate 1 review of BRD-001 (Discovery Analysis) and the feature specification derived from it. Recommendation: **Rework Required Before Approval** — full findings recorded in `reviews/GATE1-BRD-001-2026-09-15.md`. Key points: (1) separate unresolved business decisions from confirmed assumptions — specifically internal task-based orchestration, current-manager approval, and one-action-per-stakeholder must not be treated as confirmed; (2) do not encode open business/process decisions (manager approval ownership, eligibility, Payroll/IT/Facilities triggering, workflow sequence, rejection, cancellation, concurrent requests) into the spec before they are confirmed; (3) add explicit BRD/FR → Spec Requirement → Acceptance Criteria → Test Case traceability; (4) define the request lifecycle/state model only from confirmed business behavior; (5) distinguish BRD-stated requirements from discovery-inferred rules, particularly mandatory vs optional fields; (6) keep external Payroll/IT/Facilities/HRMS integration scope as TBD, not Out of Scope, while orchestration approach is unresolved; (7) define API/error contracts once the business workflow is finalized; (8) convert security considerations into explicit, testable requirements/acceptance criteria. Gate 1 Status set to "Rework Required" on all 4 active spec slices pending these items being addressed.
