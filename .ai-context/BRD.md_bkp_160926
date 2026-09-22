# Business Requirements Document (BRD)

This file is the source of truth requirements are first written down in, before they become a spec (INT SDD Blueprint §7). Spec authoring must pull from here, not the other way around.

---

### BRD-001: Digital Internal Transfer Request Journey (One-Point Employee Portal)

**Raised by:** Training/assignment brief — INT SDD methodology exercise (no named real-world business sponsor at this stage)

**Business need:** Replace the fragmented, manually-coordinated internal-transfer process (Employee ↔ Manager ↔ HR ↔ Payroll ↔ IT ↔ Facilities, conducted outside any single system) with one digital journey inside the existing One-Point Employee Portal, so an employee can request an internal transfer and track it end-to-end from a single place.

**Sponsor:** Not specified in the source document — treated as the organisation's HR function for the purpose of this exercise, pending confirmation.

**Priority:** Not specified in the source document.

**Decided:**
- Transfer requests are employee-initiated, from the portal.
- The request form captures: proposed department/business unit, proposed location, proposed role/job position, effective date, and an optional free-text reason.
- The employee can view the request's current status and see which stakeholder(s) an action is currently pending with.
- The downstream stakeholder groups in scope are: Manager, HR, Payroll, IT, Facilities.

**Open at BRD stage:** The approval/workflow sequence between stakeholders, HR eligibility rules, the conditional triggering logic for Payroll/IT/Facilities steps, notification mechanism and trigger points, cancellation/withdrawal and rejection handling, concurrent-request policy, and whether downstream stakeholder orchestration is real system integration or internal task tracking are all undecided. Full breakdown in the Discovery Analysis below.

**Notes:** This BRD is being developed as a training exercise in the INT SDD methodology (see `project_context.md` for the assignment's deliverables, timeline, and evaluation criteria). Discovery is complete and pending explicit sign-off before Spec drafting begins. Feature slug assigned: `employee-internal-transfer`.

---

## Discovery Analysis — BRD-001

### 1. Business Objective
Give employees a single, self-service digital journey in the One-Point Employee Portal to request and track an internal transfer, replacing today's fragmented, manually-coordinated process — so the employee always knows what's happening and who it's waiting on.

### 2. Business Problem
Today's process requires the employee to manually coordinate across Manager, HR, Payroll, IT and Facilities with no single source of truth: manager discussion → manager confirmation → HR eligibility check → org-data update → conditional Payroll/IT/Facilities updates → confirmation. This causes lack of transparency, reliance on informal follow-ups, and risk of a downstream step being missed (e.g., IT access not revoked) because nothing tracks the whole journey.

### 3. Primary Users / Actors
| Actor | Role in the journey | Confirmed by source? |
|---|---|---|
| Employee | Initiates the request, tracks status | Yes |
| Manager | Confirms the transfer | Yes — but *which* manager is unresolved (see Open Questions) |
| HR | Validates eligibility, updates org info | Yes |
| Payroll | Updates payroll records | Yes, conditionally ("may need") |
| IT | Provisions/removes access | Yes, conditionally |
| Facilities | Arranges new location | Yes, conditionally |

No portal admin, workflow owner, or reporting role is mentioned in the source — not assumed.

### 4. Journey Stages — Current (As-Is)
1. Employee discusses transfer with manager (informal, off-system)
2. Manager confirms transfer (mechanism not specified)
3. HR validates eligibility (criteria not specified)
4. Employee's organisational information is updated
5. Payroll updated *(conditional)*
6. IT provisions/removes access *(conditional)*
7. Facilities arranges new location *(conditional)*
8. Employee receives confirmation

Whether steps 5–7 run sequentially, in parallel, or independently is not stated.

### 5. Journey Stages — Proposed (To-Be)
1. Employee logs into the Portal and initiates an Internal Transfer Request
2. Employee completes the request: new department/BU, new location, new role/position, effective date, optional reason
3. Employee submits
4. Portal orchestrates the downstream stakeholder actions (Manager/HR/Payroll/IT/Facilities) — **mechanism not specified**
5. Employee views live status and sees which stakeholder(s) currently hold the pending action

Step 4 is the least-specified and most consequential part of the requirement.

### 6. Functional Requirements (as stated — nothing inferred)
| ID | Requirement |
|---|---|
| FR1 | Employee can initiate an Internal Transfer Request from the portal |
| FR2 | Employee can select a proposed new department/business unit |
| FR3 | Employee can select a proposed new location |
| FR4 | Employee can select a proposed new role/job position |
| FR5 | Employee can provide an effective date |
| FR6 | Employee can provide an optional free-text reason |
| FR7 | Employee can submit the request |
| FR8 | Employee can view the current status of their request |
| FR9 | Employee can view which actions are pending and with which stakeholder(s) |
| FR10 | The portal orchestrates downstream Manager/HR/Payroll/IT/Facilities activity (mechanism undefined) |

### 7. Non-Functional Requirements
None stated in the source — no performance, availability, concurrency, accessibility, or data-retention requirement given. Treated as Open Questions, not invented.

### 8. Business Rules
- "May need" (Payroll/IT/Facilities) implies these three steps are conditional, not automatic for every transfer — triggering condition undefined.
- Reason for transfer is explicitly the only optional field; all others are implicitly mandatory.
- Everything else (eligibility, notice periods, who can block a transfer, tenure) is undefined.

### 9. Data / Entities Identified (conceptual — no schema)
- **Employee** (existing — current department, location, role, manager)
- **Internal Transfer Request** (new) — requester, proposed department/BU, proposed location, proposed role, effective date, optional reason, status, submitted-at, pending-stakeholder(s)
- **Department/Business Unit**, **Location**, **Role/Job Position** — reference/master data, assumed pre-existing
- **Manager** — reference to the employee's existing organisational manager
- **Stakeholder Action/Task** — conceptual entity for a pending step owned by Manager/HR/Payroll/IT/Facilities
- **Request Status** — enumerated state, values not yet defined

### 10. External Integrations
Not specified — no HRMS, payroll system, ITSM/AD, or facilities system is named. Whether "orchestrate downstream activities" means real system integration or an internal task/checklist inside the portal's own database is undecided and materially changes the technical shape of the feature (see Decisions Required, T1).

### 11. Authentication / Authorization Requirements
Not specified. Employee login presumably already exists (Portal is pre-existing). Role-based access so each stakeholder sees only requests relevant to them is undefined, as is how portal accounts map to the five stakeholder roles.

### 12. Security Considerations
Not specified, but necessarily in play: request data is employee PII and must not be visible to unrelated employees; each stakeholder should see only requests routed to them; actions should be attributable/audited. Flagged for Tech-Lead/constitution-level confirmation before Spec, not assumed.

### 13. Reporting Requirements
None stated. Not assumed in scope.

### 14. Notifications
The current process ends with "employee receives confirmation" and the proposed process promises a "single view of progress," but no channel (email/in-portal/SMS) or trigger points are specified.

### 15. Validation Rules
Not specified. Undefined: whether proposed dept/location/role may equal the current ones; whether effective date must be in the future and by how much lead time; format/length constraints on the reason field.

### 16. Edge Cases (surfaced for awareness — not decided)
- Employee already has a pending request — can they submit another?
- Manager or HR rejects — what state does the request enter, is it resubmittable?
- Employee wants to cancel/withdraw a submitted request.
- New department has a different manager than the current one — who confirms?
- Effective date requested in the past.
- Transfer touching only role (not dept/location) — do Payroll/IT/Facilities steps auto-skip?

### 17. Dependencies
- The existing One-Point Employee Portal (auth, employee profile, and presumably Department/Location/Role master data) — this feature extends it.
- Existing employee organisational data (current dept/location/role/manager) needed to know what's actually changing.
- Possible dependency on external HR/Payroll/IT/Facilities systems — unconfirmed (ties to §10).

### 18. Assumptions (proposed — not yet confirmed)
| ID | Assumption |
|---|---|
| A1 | The One-Point Employee Portal already exists (PHP/MySQL) and already holds Employee, Department, Location, Role, and Manager reference data this feature can read |
| A2 | "Orchestrate downstream activities" means creating/tracking internal approval/task records inside the portal's own database, not integrating with real external HR/Payroll/IT/Facilities systems |
| A3 | Only the employee's *current* manager confirms the transfer — no separate "new manager" approval step |
| A4 | Each of HR/Payroll/IT/Facilities represents exactly one pending action per request |
| A5 | Once submitted, the employee can view but not edit the request |

### 19. Open Questions
1. Does Manager confirmation apply to every transfer, or only some (e.g., cross-department)?
2. Which manager confirms — current, receiving, or both?
3. What are HR's eligibility criteria?
4. What triggers Payroll vs IT vs Facilities steps — always all three, or conditional on what changed?
5. Is the approval sequence strictly sequential, or can steps run in parallel once HR validates?
6. Can an employee cancel/withdraw a request, and up to what stage?
7. What happens on rejection at any stage — terminated, or resubmittable/appealable?
8. Can an employee have more than one active request at a time?
9. Is there a minimum lead time for the effective date?
10. Are notifications required, via what channel, at which trigger points?
11. Does this apply to all employee types (permanent/contract/probation)?
12. Who owns turnaround-time (SLA) expectations for each stakeholder step?
13. Is Payroll/IT/Facilities orchestration real system integration, or internal task tracking?
14. How do portal accounts map to the Manager/HR/Payroll/IT/Facilities roles for access control?

### 20. Decisions Required — Business vs Technical

**Business decisions (need a business/process owner):**
| # | Decision | Ties to |
|---|---|---|
| B1 | Eligibility rules for a transfer | Q3 |
| B2 | Which manager(s) must confirm | Q1, Q2 |
| B3 | Conditional logic for Payroll/IT/Facilities triggering | Q4 |
| B4 | Approval sequencing (sequential vs parallel) | Q5 |
| B5 | Cancellation/withdrawal policy | Q6 |
| B6 | Rejection handling policy | Q7 |
| B7 | Concurrent-request policy | Q8 |
| B8 | Minimum lead time for effective date | Q9 |
| B9 | Notification requirements | Q10 |
| B10 | Employee-type scope | Q11 |

**Technical decisions (deferred to Plan/Architecture, Gate 1 — not Discovery/Spec):**
| # | Decision | Ties to |
|---|---|---|
| T1 | Internal task/workflow model vs external system integration for Payroll/IT/Facilities | Q13, A2 |
| T2 | Notification delivery mechanism | Q10 |
| T3 | How Department/Location/Role/Manager master data is sourced | A1 |
| T4 | Authentication/session model this feature plugs into | §11 |
| T5 | Request/task state-machine design | §9, §16 |

### 21. Out of Scope (proposed — pending confirmation)
- Real-time integration with external Payroll/IT/Facilities/HRMS systems (superseded by A2 unless confirmed otherwise)
- Multi-level approval chains beyond Manager + HR + Payroll + IT + Facilities
- Reporting/analytics dashboards
- Mobile app (web only, per project stack)
- Creation/management UI for Employee, Department, Location, Role master data (assumed pre-existing)
