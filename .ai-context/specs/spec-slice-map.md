# Spec Slice Map

## Parent Feature
Employee Internal Transfer Request (One-Point Employee Portal)

## Source Spec
`.ai-context/specs/employee-internal-transfer.spec.md` (Draft v1.0 — Ready for Gate 1 Peer Review, monolithic). This spec is superseded by the slices below and is retained as historical/reference input, per the constitution's "deprecated specs are archived, not deleted" convention — it is not deleted, and its slug does not get reused.

## BRD References
BRD.md#BRD-001 (FR1–FR10, AC1–AC9, all sections of the Discovery Analysis)

## Naming Convention Note
Following the convention already established in this workspace (Spec ID = feature slug, e.g. the source spec's `## Spec ID` = `employee-internal-transfer`), each slice's Spec ID is its own slug — there is no separate numeric `SPEC-NNN` identifier. The `Sequence` column below expresses build/read order.

## Slice Sequence

| Sequence | Spec ID (slug) | Business Outcome | Depends On | Produces |
|---|---|---|---|---|
| 01 | `employee-transfer-request-submission` | Employee captures and submits a new transfer request | — (entry point) | A Transfer Request record, Status = "Submitted" |
| 02 | `employee-transfer-stakeholder-orchestration` | On submission, one pending task per stakeholder group is generated | 01 | 5 pending Stakeholder Task records against the request |
| 03 | `employee-transfer-status-tracking` | Employee views a request's live status and which stakeholders are still pending | 01, 02 (reads outcomes of 04 at runtime) | Read-only status/detail/list views |
| 04 | `employee-transfer-stakeholder-task-management` | Stakeholder views and completes their own pending task | 02 | Task state transitions (Pending → Completed), consumed by 03 |

## Process Flow

```text
BRD-001
  |
  v
employee-transfer-request-submission (01)
  |
  v
employee-transfer-stakeholder-orchestration (02)
  |
  +-------------------------------+
  |                                |
  v                                v
employee-transfer-status-tracking (03)   employee-transfer-stakeholder-task-management (04)
  ^                                          |
  |                                          |
  +---------- reads task-state updates ------+
```

03 and 04 both depend on 02 having generated the stakeholder tasks. 03 is a pure read path; 04 is the only spec that mutates task state. 03's `pending_stakeholders` output is only accurate once 04 has run for a given task — this is a **runtime data dependency**, not a build-sequence dependency: 03 and 04 can be implemented and reviewed independently, in either order, since neither one's contract depends on the other's implementation, only on Spec 02's data model.

## Cross-Spec Requirements

### CSR-001 — Authenticated session required
Every endpoint in every slice requires an authenticated Portal session (constitution.md, Security Posture: "Every state-changing endpoint requires an authenticated session"). Applicable to all four specs; each spec's own 401 exception row is the local expression of this rule, not a duplicate requirement.

### CSR-002 — Per-caller access scoping
No request or task data is disclosed to a caller who is neither the owning employee nor an assigned stakeholder for that specific request/task (constitution.md, Security Posture: "each stakeholder view must only return requests routed to that stakeholder"). Expressed locally as:
- `employee-transfer-status-tracking.AC3` (view access)
- `employee-transfer-stakeholder-task-management.AC2` (act access)

## Shared Requirements
- **Stakeholder Task** is a data entity introduced by `employee-transfer-stakeholder-orchestration` (02) and is read/written by `employee-transfer-status-tracking` (03, read-only) and `employee-transfer-stakeholder-task-management` (04, read/write). It is not redefined in each spec — 03 and 04 both reference 02's Data Requirements section rather than restating the entity shape.
- **Business Decisions B1–B10** (BRD.md §20) are carried forward, each attached only to the slice(s) they actually affect — see Requirements Coverage below and each slice's own Risks / Open Decisions section. They are not restated in every slice.

## Requirements Coverage

| Original Requirement / AC | New Spec | New AC ID(s) |
|---|---|---|
| FR1–FR7: initiate, capture dept/location/role/date/reason, submit | `employee-transfer-request-submission` | AC1, AC2, AC3 |
| Original AC1 — required fields + optional reason | `employee-transfer-request-submission` | AC1 |
| Original AC2 — validation error on missing required field | `employee-transfer-request-submission` | AC2 |
| Original AC3 — reason persisted and returned | `employee-transfer-request-submission` | AC3 |
| FR10: portal orchestrates downstream Manager/HR/Payroll/IT/Facilities activity | `employee-transfer-stakeholder-orchestration` | AC1 |
| Original AC4 — freshly submitted request has Status "Submitted" and all 5 stakeholders pending | `employee-transfer-stakeholder-orchestration` | AC1 |
| FR8: employee views current status | `employee-transfer-status-tracking` | AC1, AC2 |
| FR9: employee sees which stakeholder(s) pending | `employee-transfer-status-tracking` | AC1 |
| Original AC5 — pending_stakeholders reflects partial completion | `employee-transfer-status-tracking` | AC1 |
| Original AC7 — overall status reflects full completion | `employee-transfer-status-tracking` | AC2 |
| Original AC8 (view half) — 403, no data disclosed to unrelated caller viewing | `employee-transfer-status-tracking` | AC3 |
| Original AC6 — assigned stakeholder completes own task | `employee-transfer-stakeholder-task-management` | AC1 |
| Original AC8 (act half) — 403 when caller isn't the assigned stakeholder | `employee-transfer-stakeholder-task-management` | AC2 |
| Original AC9 — 409 on completing an already-completed task | `employee-transfer-stakeholder-task-management` | AC3 |

### Correction made during slicing (not a new requirement)
The source spec's Unit Test Cases table mapped **UT08** ("Non-assigned stakeholder calls complete on someone else's task → 403") to **AC6**, even though that scenario is an access-control case matching the intent of the source spec's **AC8** ("neither the owning employee nor an assigned stakeholder... returns 403"), not AC6 ("assigned stakeholder calls complete"). This slicing resolves the mismatch by giving the access-control scenario its own AC (`employee-transfer-stakeholder-task-management.AC2`) and mapping the equivalent test there (`employee-transfer-stakeholder-task-management.UT02`). No behavior was invented — this is a traceability fix, carried forward for Gate 1 to confirm.

### Unmapped Requirements
None. All nine original Acceptance Criteria, all five original API endpoints, and all eleven original Unit Test Cases are accounted for above.

### Business Decisions (BRD.md §20, B1–B10) — attached per affected slice
| ID | Decision | Attached To |
|---|---|---|
| B1 | HR eligibility rules | `employee-transfer-request-submission` (Risks) |
| B2 | Which manager(s) confirm | `employee-transfer-stakeholder-orchestration` (Risks) |
| B3 | Conditional Payroll/IT/Facilities triggering | `employee-transfer-stakeholder-orchestration` (Risks) |
| B4 | Approval sequencing | `employee-transfer-stakeholder-orchestration` (Risks) |
| B5 | Cancellation/withdrawal policy | `employee-transfer-request-submission` (Out of Scope) |
| B6 | Rejection handling policy | `employee-transfer-stakeholder-task-management` (Out of Scope) |
| B7 | Concurrent-request policy | `employee-transfer-request-submission` (Out of Scope) |
| B8 | Minimum lead time for effective date | `employee-transfer-request-submission` (Out of Scope) |
| B9 | Notification requirements | `employee-transfer-status-tracking` (Out of Scope) |
| B10 | Employee-type scope | `employee-transfer-request-submission` (Out of Scope) |

## Status
All four slices are **Draft**, not Approved. They enter Gate 1 individually and independently.
