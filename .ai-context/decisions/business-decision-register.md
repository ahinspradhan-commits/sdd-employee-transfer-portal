# Business Decision Register — Employee Internal Transfer

**Purpose:** Resolve business-level ambiguity before workflow-dependent specifications are baselined.

**Important:** The supplied assessment document confirms the journey and actors, but it does **not** answer the decisions below. The entries marked **PROPOSED** are assessment working decisions and must be explicitly accepted by the project owner/business proxy before they are treated as requirements.

| ID | Decision | Proposed v1 baseline | Status | Affected specs |
|---|---|---|---|---|
| BD-001 | Which manager confirms? | Current/line manager at time of submission | PROPOSED — approval required | Orchestration, Task Management |
| BD-002 | Is manager confirmation required? | Yes, for every transfer request | PROPOSED — approval required | Orchestration |
| BD-003 | HR eligibility rules | HR validates eligibility after manager confirmation; exact eligibility criteria are outside this assessment and represented as a stakeholder action, not automated business-rule validation | PROPOSED — approval required | Orchestration, Status |
| BD-004 | When is Payroll required? | Payroll task is created when the approved transfer changes payroll-relevant organisational attributes; for the assessment, this is represented by a deterministic configuration flag rather than guessed business rules | PROPOSED — approval required | Orchestration |
| BD-005 | When is IT required? | IT task is created when the transfer requires access/provisioning changes; for the assessment, represented by a deterministic configuration flag | PROPOSED — approval required | Orchestration |
| BD-006 | When is Facilities required? | Facilities task is created when the transfer changes work location; for the assessment, represented by a deterministic configuration flag | PROPOSED — approval required | Orchestration |
| BD-007 | Sequence | Manager → HR first; conditional Payroll/IT/Facilities may run after HR approval and in parallel | PROPOSED — approval required | Orchestration, Status |
| BD-008 | Rejection | A stakeholder may reject; request becomes Rejected and no later stakeholder tasks are actionable | PROPOSED — approval required | Task Management, Status |
| BD-009 | Cancellation | Employee may withdraw while request is not Completed/Rejected | PROPOSED — approval required | Submission, Status |
| BD-010 | Concurrent requests | One active transfer request per employee | PROPOSED — approval required | Submission |
| BD-011 | Effective date | Must be today or later; no additional lead-time rule is assumed | PROPOSED — approval required | Submission |
| BD-012 | Notifications | In-portal status is mandatory; email/SMS is not implemented in this assessment | PROPOSED — approval required | Status |
| BD-013 | Eligible employee types | Authenticated employees in the existing portal are eligible unless an explicit employee-type restriction is supplied | PROPOSED — approval required | Submission |
| BD-014 | SLA | No business SLA is supplied; no SLA enforcement is implemented | PROPOSED — approval required | Status |

## Approval rule

Do not copy any **PROPOSED** value into a normative specification until the decision is explicitly accepted and recorded in the project decision log/Gate 1 record.

## Why this register exists

The source requirement confirms the employee journey and the involvement of Manager, HR, Payroll, IT and Facilities, but leaves workflow sequencing, triggering conditions, rejection, cancellation, concurrency and validation rules undefined. Requirements traceability is stronger when each requirement can be traced to its source and when derived decisions are explicitly identified rather than silently introduced. citeturn0search0turn0search4
