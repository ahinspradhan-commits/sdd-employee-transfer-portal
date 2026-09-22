# Business Requirements Document (BRD)

## BRD-001: Digital Internal Transfer Request Journey

**Feature:** Employee Internal Transfer Digital Journey  
**System:** One-Point Employee Portal  
**Source:** SDD Developer Assessment — Employee Internal Transfer Digital Journey  
**Status:** Revised after BRD Gate 1 review  
**Feature slug:** `employee-internal-transfer`

---

## 1. Requirement Source and Business Need

The source requirement states that the organisation wants a single digital journey through the One-Point Employee Portal for an employee to initiate and track an internal transfer.

The current journey involves multiple teams and systems:

1. Employee discusses the transfer with the manager.
2. Manager confirms the transfer.
3. HR validates eligibility.
4. Employee organisational information is updated.
5. Payroll may need to be updated.
6. IT may need to provision or remove access.
7. Facilities may need to arrange the new location.
8. Employee receives confirmation.

The business need is to replace this fragmented process with one portal journey that provides a single view of progress.

---

## 2. Business Objective

Provide employees with a single digital journey in the One-Point Employee Portal to:

- initiate an internal transfer request;
- provide the proposed transfer information;
- submit the request;
- view the current request status; and
- see which stakeholder action is pending.

The portal should orchestrate the downstream activities described in the source requirement and provide the employee with a single view of progress.

**Important:** The source requirement does not define how orchestration is technically implemented. That decision must not be invented in the BRD.

---

## 3. Scope

### 3.1 In Scope — Confirmed by Source

| ID | Scope item | Source status |
|---|---|---|
| S-01 | Employee initiates an Internal Transfer Request from the One-Point Employee Portal | Confirmed |
| S-02 | Employee selects proposed department/business unit | Confirmed |
| S-03 | Employee selects proposed location | Confirmed |
| S-04 | Employee selects proposed role/job position | Confirmed |
| S-05 | Employee provides an effective date | Confirmed |
| S-06 | Employee provides an optional reason | Confirmed |
| S-07 | Employee submits the request | Confirmed |
| S-08 | Employee views current request status | Confirmed |
| S-09 | Employee views pending actions and responsible stakeholders | Confirmed |
| S-10 | Portal provides a single view of progress | Confirmed |
| S-11 | Downstream activities involve Manager, HR, Payroll, IT and Facilities | Confirmed, with Payroll/IT/Facilities described as conditional |

### 3.2 Not Yet Defined

The following are not defined by the source requirement and therefore must not be presented as confirmed requirements:

- approval sequence;
- manager selection rule;
- HR eligibility criteria;
- conditions for Payroll, IT and Facilities;
- rejection behaviour;
- cancellation/withdrawal;
- concurrent active requests;
- effective-date lead time;
- notification channels and trigger points;
- employee-type eligibility;
- stakeholder SLAs;
- integration mechanism;
- role/access mapping.

---

## 4. Actors

| Actor | Responsibility stated by source | Status |
|---|---|---|
| Employee | Initiates request, provides information, submits request, tracks status | Confirmed |
| Manager | Confirms transfer | Confirmed |
| HR | Validates eligibility and organisational information is updated | Confirmed |
| Payroll | May need to update payroll | Confirmed as conditional downstream stakeholder |
| IT | May need to provision/remove access | Confirmed as conditional downstream stakeholder |
| Facilities | May need to arrange new location | Confirmed as conditional downstream stakeholder |

No separate portal administrator, workflow administrator, reporting user, or business owner is defined by the source requirement.

---

## 5. Current-State Journey

1. Employee discusses transfer with manager.
2. Manager confirms the transfer.
3. HR validates eligibility.
4. Employee organisational information is updated.
5. Payroll may be updated.
6. IT may provision or remove access.
7. Facilities may arrange the new location.
8. Employee receives confirmation.

The source does not define whether Payroll, IT and Facilities execute sequentially, in parallel, or independently.

---

## 6. Proposed Business Journey

### Stage 1 — Initiate

Employee accesses the One-Point Employee Portal and starts an Internal Transfer Request.

### Stage 2 — Provide Transfer Details

Employee provides:

- proposed department/business unit;
- proposed location;
- proposed role/job position;
- effective date;
- optional reason.

### Stage 3 — Submit

Employee submits the request.

### Stage 4 — Stakeholder Processing

The portal orchestrates the downstream activities involving the relevant stakeholders:

- Manager;
- HR;
- Payroll, where applicable;
- IT, where applicable;
- Facilities, where applicable.

**The business requirement confirms orchestration but does not define the workflow sequence or technical integration mechanism.**

### Stage 5 — Track Progress

Employee can view:

- current request status; and
- which stakeholder action(s) are pending.

### Stage 6 — Completion

The employee receives confirmation when the transfer journey reaches completion.

**The exact completion state and confirmation mechanism remain undefined by the source.**

---

## 7. Functional Business Requirements

| ID | Requirement | Priority | Status |
|---|---|---|---|
| BR-001 | Employee shall be able to initiate an Internal Transfer Request from the One-Point Employee Portal. | Must | Confirmed |
| BR-002 | Employee shall be able to select the proposed department/business unit. | Must | Confirmed |
| BR-003 | Employee shall be able to select the proposed location. | Must | Confirmed |
| BR-004 | Employee shall be able to select the proposed role/job position. | Must | Confirmed |
| BR-005 | Employee shall be able to provide an effective date. | Must | Confirmed |
| BR-006 | Employee shall be able to provide an optional reason for the transfer. | Optional | Confirmed |
| BR-007 | Employee shall be able to submit the Internal Transfer Request. | Must | Confirmed |
| BR-008 | Employee shall be able to view the current status of their request. | Must | Confirmed |
| BR-009 | Employee shall be able to view which stakeholder action(s) are pending. | Must | Confirmed |
| BR-010 | The portal shall orchestrate the downstream activities associated with the transfer journey. | Must | Confirmed; workflow details open |
| BR-011 | The journey shall provide the employee with a single view of transfer progress. | Must | Confirmed |

---

## 8. Confirmed Business Rules

Only rules supported directly by the source are recorded as confirmed.

| ID | Rule |
|---|---|
| RULE-001 | The transfer request is initiated by the employee. |
| RULE-002 | The request contains proposed department/business unit, proposed location, proposed role/job position and effective date. |
| RULE-003 | The reason for transfer is optional. |
| RULE-004 | The employee can view current request status. |
| RULE-005 | The employee can view pending stakeholder actions. |
| RULE-006 | Manager and HR participate in the transfer journey. |
| RULE-007 | Payroll, IT and Facilities may participate depending on the transfer circumstances. |

### Business rules that require confirmation

The following must not be converted into specification rules until a business/process owner confirms them:

- who approves the transfer;
- whether current manager, receiving manager, or both are required;
- HR eligibility criteria;
- conditions that trigger Payroll;
- conditions that trigger IT;
- conditions that trigger Facilities;
- workflow sequencing;
- cancellation policy;
- rejection policy;
- concurrent-request policy;
- effective-date rules;
- employee-type eligibility;
- SLA/turnaround expectations.

---

## 9. Open Business Decisions

| ID | Decision required | Why it matters | Blocking? |
|---|---|---|---|
| BD-001 | Which manager confirms the transfer? | Determines approval ownership | Yes |
| BD-002 | Is manager confirmation required for every transfer? | Determines workflow entry conditions | Yes |
| BD-003 | What are HR eligibility rules? | Determines whether a request can proceed | Yes |
| BD-004 | When are Payroll activities required? | Determines downstream routing | Yes |
| BD-005 | When are IT activities required? | Determines downstream routing | Yes |
| BD-006 | When are Facilities activities required? | Determines downstream routing | Yes |
| BD-007 | Are stakeholder activities sequential or parallel? | Determines business workflow | Yes |
| BD-008 | What happens when a stakeholder rejects the request? | Determines terminal/resubmission behaviour | Yes |
| BD-009 | Can the employee cancel/withdraw a request? | Determines lifecycle behaviour | Yes |
| BD-010 | Can an employee have multiple active transfer requests? | Determines concurrency policy | Yes |
| BD-011 | What effective-date restrictions apply? | Determines validation/business rules | Yes |
| BD-012 | Are notifications required, and at which business events? | Determines communication requirements | Yes |
| BD-013 | Which employee types are eligible? | Determines scope | Yes |
| BD-014 | What turnaround/SLA expectations apply to stakeholders? | Determines operational expectations | No |

---

## 10. Technical Decisions — Separate From Business Decisions

The following should be resolved during technical planning/architecture rather than presented as business requirements:

| ID | Technical decision |
|---|---|
| TD-001 | Whether downstream activity is implemented through internal portal tasks or external-system integrations, after the business scope is confirmed. |
| TD-002 | How existing portal authentication is reused. |
| TD-003 | How stakeholder roles and permissions are technically mapped. |
| TD-004 | How department, location, role and manager master data are technically sourced. |
| TD-005 | Database/data model for requests, stakeholder actions and status history. |
| TD-006 | API/service boundaries, if APIs are required. |
| TD-007 | Notification delivery technology, once notification requirements are confirmed. |
| TD-008 | Technical state-machine implementation, once business lifecycle states are confirmed. |
| TD-009 | Audit logging implementation. |
| TD-010 | Error handling, retry and integration-failure mechanisms if external integrations are approved. |

**Correction from the previous BRD:** Whether the organisation wants real external-system integration or only portal-based task tracking is partly a scope/business decision. The technical implementation choice comes after that business decision.

---

## 11. Assumptions

Assumptions are explicitly separated from confirmed requirements.

| ID | Assumption | Impact if false | Must confirm? |
|---|---|---|---|
| A-001 | The One-Point Employee Portal already exists and provides authenticated employee access. | Authentication and portal scope change | Yes |
| A-002 | Existing employee organisational information is available to support the journey. | Additional data source/integration may be required | Yes |
| A-003 | Department, location and role/job-position data are available from an existing source. | Master-data scope may increase | Yes |

The following assumptions from the previous BRD have been removed because they prematurely decide business behaviour:

- "Only the current manager confirms."
- "Each HR/Payroll/IT/Facilities represents exactly one pending action."
- "Once submitted, the employee can view but not edit."
- "Orchestration means internal database task tracking."

These are not established by the source requirement.

---

## 12. Validation Requirements

### Confirmed

The source confirms that:

- department/business unit is captured;
- location is captured;
- role/job position is captured;
- effective date is captured;
- reason is optional.

### Not defined

The source does not define:

- whether proposed values may equal current values;
- whether effective date must be future-dated;
- minimum lead time;
- maximum reason length;
- allowed characters;
- whether fields are validated against master data;
- validation/error-message behaviour.

These must be resolved before they become specification-level acceptance criteria.

---

## 13. Status Model

The source requires the employee to view a "current status" but does not define status values.

Therefore, the following must **not** be treated as confirmed status values:

- Draft;
- Submitted;
- Manager Pending;
- HR Pending;
- Payroll Pending;
- IT Pending;
- Facilities Pending;
- Completed;
- Rejected;
- Cancelled.

These are candidate design states only and require business confirmation before being used in the specification.

---

## 14. Notifications

The source indicates that the employee receives confirmation, but does not define:

- notification channel;
- notification events;
- recipient groups;
- notification content;
- failure/retry behaviour.

Therefore notification requirements remain an open business decision.

---

## 15. Security and Access — Requirement Boundary

The source does not provide detailed security requirements.

The following are therefore discovery concerns rather than confirmed BRD requirements:

- employees must not see another employee's request;
- stakeholders should only access requests assigned/relevant to them;
- actions should be attributable to an authenticated user;
- transfer activity may require an audit trail.

These should be validated and then converted into explicit security requirements before implementation.

---

## 16. Edge Cases Requiring Business Decisions

1. Employee already has an active transfer request.
2. Manager rejects the request.
3. HR rejects the request.
4. A downstream stakeholder rejects or cannot complete its action.
5. Employee attempts to cancel after submission.
6. Effective date is in the past.
7. Effective date is too soon.
8. Proposed department, location or role is unchanged.
9. Current and receiving managers are different.
10. Payroll, IT or Facilities is not required.
11. A downstream activity remains pending beyond its expected turnaround time.

These are deliberately recorded as unresolved scenarios rather than invented behaviour.

---

## 17. Dependencies

### Confirmed / implied by source

- One-Point Employee Portal.
- Employee identity/profile information.
- Organisational information required for the transfer journey.

### Potential dependencies requiring confirmation

- HR system;
- Payroll system;
- IT/access-management system;
- Facilities system;
- department/location/role master data sources;
- notification service.

No specific external system is named by the source requirement.

---

## 18. Out of Scope

Only items that are clearly outside the supplied business requirement should be treated as out of scope.

### Not requested by the source

- Reporting/analytics dashboards.
- Mobile application.
- Administration UI for maintaining employee master data.
- Unrelated employee-service journeys.
- Features unrelated to internal transfer processing.

### Do NOT mark the following as definitively out of scope yet

- HRMS integration.
- Payroll integration.
- IT integration.
- Facilities integration.

The source explicitly says the portal should orchestrate downstream activities, but does not specify whether this means real integrations or portal-based task orchestration. Therefore this must remain an open scope/architecture decision.

---

## 19. Traceability to Source Requirement

| BRD requirement | Source statement |
|---|---|
| BR-001 | Employee can initiate an Internal Transfer Request |
| BR-002 | Select proposed new department/business unit |
| BR-003 | Select proposed new location |
| BR-004 | Select proposed role/job position |
| BR-005 | Provide effective date |
| BR-006 | Provide optional reason |
| BR-007 | Submit request |
| BR-008 | View current status |
| BR-009 | View pending stakeholder actions |
| BR-010 | Portal orchestrates downstream activities |
| BR-011 | Single view of progress |

---

## 20. Gate 1 Readiness

### Confirmed and ready to proceed

The following business scope is sufficiently clear to form the initial specification baseline:

- employee initiation;
- transfer information captured;
- submission;
- status visibility;
- pending stakeholder visibility;
- downstream stakeholder groups;
- single progress view.

### Blocking decisions before finalising workflow-dependent specification

The following require explicit business-owner decisions:

1. Manager approval ownership.
2. HR eligibility criteria.
3. Payroll/IT/Facilities triggering conditions.
4. Workflow sequence.
5. Rejection handling.
6. Cancellation handling.
7. Concurrent-request policy.
8. Effective-date rules.
9. Notification requirements.
10. Eligible employee types.

### Gate 1 principle

No unresolved business decision should be silently converted into a technical implementation rule, acceptance criterion, API behaviour, status value, or database constraint.

Once the blocking business decisions are confirmed, the approved BRD becomes the source for the Feature Specification, Acceptance Criteria, API Contract and Spec-Derived Test Cases.
