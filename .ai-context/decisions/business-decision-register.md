# Business Decision Register — Employee Internal Transfer

**Version:** v2.0 (2026-09-28): restructured for Gate 1 follow-up G1R2-FA-05
**Purpose:** Track every business decision the source requirement leaves open, who owns it, and whether it is confirmed. Workflow-dependent specification items stay **Blocked** until the decision that governs them is **Confirmed** here.

## Rules

1. The **Proposed option** column is a discussion starting point only. It is **not normative**. It must not appear in a spec as a requirement, an AC, an API behaviour, a status value or a database constraint while the status is anything other than **Confirmed**.
2. A decision becomes **Confirmed** only when the named approver accepts it and the evidence (meeting minute, email or signed comment) is recorded in the History section below.
3. On confirmation: update this register, then the BRD (if the rule becomes a BRD rule), then the affected specs (Blocked → Baseline), then `traceability.md`.
4. The Project Owner may propose options but may not self-approve a business decision.

## Status Values

| Status | Meaning |
|---|---|
| Open | Question raised; no owner response yet |
| Proposed | An option is tabled for the owner; not accepted |
| Confirmed | Accepted by the named approver, with evidence recorded |
| Rejected | Proposed option declined; a new option is required |
| Deferred | Owner agrees it is out of v1 scope; spec records it as out of scope |

## Register

**Owner role** is the business function expected to decide. **Named approver** is the individual who signs off. Named approvers are still **TBD**; the Project Owner must nominate them with the Gate 1 Reviewer before the specification Gate 1 review.

| ID | Decision | Owner role | Named approver | Proposed option (non-normative) | Status | Blocks (spec items) |
|---|---|---|---|---|---|---|
| BD-001 | Which manager confirms the transfer (current, receiving, or both)? | HR Process Owner | TBD | Current/line manager at submission | Proposed | ORC-SR-02, ACT-SR-01, BAR-004, BAR-005 |
| BD-002 | Is manager confirmation required for every transfer? | HR Process Owner | TBD | Yes, always | Proposed | ORC-SR-02 |
| BD-003 | HR eligibility criteria | HR Process Owner | TBD | HR validates manually as a stakeholder action; no automated eligibility rules | Proposed | SUB-SR-07, ORC-SR-02 |
| BD-004 | When are Payroll activities required? | Payroll Owner | TBD | When payroll-relevant attributes change (criteria TBD) | Proposed | ORC-SR-03, ACT-SR-01, BAR-004 |
| BD-005 | When are IT activities required? | IT Service Owner | TBD | When access/provisioning changes are needed (criteria TBD) | Proposed | ORC-SR-03, ACT-SR-01, BAR-004 |
| BD-006 | When are Facilities activities required? | Facilities Owner | TBD | When work location changes | Proposed | ORC-SR-03, ACT-SR-01, BAR-004 |
| BD-007 | Are stakeholder activities sequential or parallel? | HR Process Owner | TBD | Manager → HR, then Payroll/IT/Facilities in parallel | Proposed | ORC-SR-04, ACT-SR-05, request transitions (TRK §5) |
| BD-008 | What happens when a stakeholder rejects? | HR Process Owner | TBD | Request ends as rejected; later actions not actionable | Proposed | ACT-SR-02 (outcome values), ACT-SR-05, ORC-SR-05 |
| BD-009 | Can the employee cancel/withdraw? | HR Process Owner | TBD | Yes, until completion or rejection | Proposed | SUB-SR-09 (reserved), status transitions |
| BD-010 | Can an employee have multiple active requests? | HR Process Owner | TBD | One active request per employee | Proposed | SUB-SR-08 |
| BD-011 | Effective-date restrictions | HR Process Owner | TBD | Today or later; no lead time | Proposed | SUB-SR-06 |
| BD-012 | Are notifications required, and at which events? | HR Process Owner | TBD | In-portal status only for v1 | Proposed | TRK-SR-06 (reserved) |
| BD-013 | Which employee types are eligible? | HR Process Owner | TBD | All authenticated portal employees | Proposed | SUB-SR-07 |
| BD-014 | Stakeholder turnaround/SLA | HR Process Owner | TBD | No SLA in v1 | Proposed | None (non-blocking) |
| BD-015 | Employee-visible status values and the definition of "completion" | HR Process Owner | TBD | None tabled | Open | SUB-SR-05 (status value), TRK-SR-04, TRK-SR-05, ORC-SR-05 |
| BD-016 | Business validation of proposed values (may equal current; inactive values; reason length limit) | HR Process Owner | TBD | None tabled | Open | SUB-SR-04 |
| BD-017 | Who, beyond the requester, may view a request and act on each action | HR Process Owner with Information Security | TBD | None tabled | Open | BAR-001..005 confirmation, TRK-SR-03b, ACT-SR-01 |
| BD-018 | Is an audit trail a business/compliance requirement, and what must it retain? | HR Process Owner with Compliance | TBD | None tabled | Open | BAR-006, ACT-SR-04b |
| BD-019 | Real downstream system integration vs portal-based tracking of stakeholder actions | Business Sponsor with IT Architecture | TBD | None tabled | Open | ORC-SR-01b, ACT-SR-01, ACT-API-01/02, TD-001, Technical Plan |

## Summary

| Status | Count | IDs |
|---|---|---|
| Confirmed | 0 | — |
| Proposed | 14 | BD-001..BD-014 |
| Open | 5 | BD-015..BD-019 |

**No business decision is confirmed.** Every workflow-dependent spec item is therefore Blocked.

## History

| Date | ID(s) | Event | By | Evidence |
|---|---|---|---|---|
| 2026-09-16 | BD-001..014 | Raised in BRD v2.0; proposed options tabled | Ahin Subhra Pradhan (Project Owner) | BRD.md §9 |
| 2026-09-28 | All | Gate 1 review: decisions to be tracked with owners and confirmation status; not to be converted into implementation rules without approval | Sourav Kumar Maity (Gate 1 Reviewer) | reviews/GATE1-BRD-002-2026-09-28.md |
| 2026-09-28 | BD-015..019 | Raised from BRD §6, §10, §12, §13, §15, §18 | Project Owner | BRD.md v2.1 §9 |
