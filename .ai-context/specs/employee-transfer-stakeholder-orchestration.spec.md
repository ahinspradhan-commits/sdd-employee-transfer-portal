# Spec: Employee Transfer Stakeholder Orchestration

## Spec ID
employee-transfer-stakeholder-orchestration (short code **ORC**)

## Status
Draft, ready for Gate 1 specification review. **Mostly Blocked** by business decisions.

## Version
v2.0 (2026-09-28): re-baselined against BRD-001 v2.1. Supersedes v1.0. The v1.0 rule "exactly five tasks, created in parallel, current manager only" is withdrawn.

## Governance

| Role | Name |
|---|---|
| Project Owner | Ahin Subhra Pradhan |
| Gate 1 Reviewer | Sourav Kumar Maity |
| Gate 2 Reviewer | TBD (independent of implementer) |

### Gate 1 Status
Requested (specification review pending)

### Gate 2 Status
Not Requested

## Linked BRD
`.ai-context/BRD.md#BRD-001` v2.1: BR-010, BR-011, RULE-006, RULE-007, §6 Stage 4, BD-001..008, BD-015, BD-019

## Parent Feature
See `spec-slice-map.md` v2.0. Slice sequence **02**.

---

## 1. Intent
Once a request is submitted, the portal records the stakeholder actions that the transfer requires, each attributed to a stakeholder group. This lets the employee see what is pending and with whom (BR-009, BR-011), and lets the responsible stakeholder record its outcome (ACT).

**What is confirmed:** the stakeholder groups involved (Manager and HR; Payroll, IT and Facilities where applicable), and that the portal orchestrates them (BR-010).
**What is not confirmed:** which actions a given request needs, who is responsible for each, in what order, how the request-level outcome is derived, and whether actions are executed in external systems (BD-001..008, BD-015, BD-019).

This spec therefore sets the **record contract** as Baseline and leaves the **routing rules** Blocked.

## 2. Scope

### In scope (Baseline)
- The stakeholder-action record and its minimal lifecycle: pending, then outcome recorded.
- The stakeholder group vocabulary.
- Atomic creation together with the request.

### Blocked
- Which actions are created for a request (BD-001..006).
- Ordering and activation of actions (BD-007).
- The effect of an outcome on other actions and on the request (BD-008, BD-015).
- The execution mechanism, portal-based or external integration (BD-019 → TD-001).

### Out of scope
- SLA tracking/escalation (BD-014, non-blocking; the proposed option is no SLA).

## 3. Entry / Exit Conditions

| | Condition | Tag |
|---|---|---|
| Entry | SUB has validated a submission and is inside the submission transaction | Baseline |
| Exit | The request and its initial stakeholder actions are committed together, or neither is | Baseline |
| Exit | The **set** of initial actions matches the confirmed routing rules | Blocked (BD-001..007) |

## 4. Spec Requirements

| ID | Requirement | Trace | Tag |
|---|---|---|---|
| ORC-SR-01a | For every submitted request the portal holds a record of each stakeholder action: the stakeholder group, whether it is pending, and (once recorded) its outcome, outcome time and recorder. This record is the single source for TRK and ACT. | BR-009, BR-010, BR-011 | Baseline |
| ORC-SR-01b | How each action is executed: by a portal user recording it, or by exchange with an external HR/Payroll/IT/Facilities system. | BD-019, TD-001 | **Blocked (BD-019)** |
| ORC-SR-02 | Whether Manager and HR actions are created for a request, and which manager is responsible. | RULE-006, BD-001, BD-002, BD-003 | **Blocked (BD-001, BD-002, BD-003)** |
| ORC-SR-03 | The conditions under which Payroll, IT and Facilities actions are created. | RULE-007, BD-004, BD-005, BD-006 | **Blocked (BD-004..006)** |
| ORC-SR-04 | Ordering: which actions are actionable immediately and which wait for others. | BD-007 | **Blocked (BD-007)** |
| ORC-SR-05 | How the request-level status and outcome (completion, rejection) are derived from action outcomes. | BD-008, BD-015 | **Blocked (BD-008, BD-015)** |
| ORC-SR-06 | Creating the request and its initial stakeholder actions is a single atomic unit. No observer (TRK, ACT) can see a request without its initial actions. | Constitution §4 integrity; SUB-FS-02 | Baseline |
| ORC-SR-07 | `stakeholder_group` takes exactly one of: `Manager`, `HR`, `Payroll`, `IT`, `Facilities`. | RULE-006, RULE-007, source §2 | Baseline |

### Design note (non-normative, for the Plan)
Put the Blocked rules (ORC-SR-02..05) behind a single replaceable **routing policy** component. Baseline tests use a fixture policy that returns a fixed action set. Confirming BD-001..008 then changes the policy and its tests, not the SUB, TRK or ACT contracts. Candidate ADR: ADR-0001 "Routing policy boundary".

## 5. State Model — Stakeholder Action

| From | Trigger | To | Tag |
|---|---|---|---|
| (none) | Request submitted; routing policy selects this action | `pending = true` | Baseline mechanism; **which** actions → Blocked (ORC-SR-02/03) |
| `pending = true` | Responsible stakeholder records outcome (ACT) | `pending = false`, `outcome` set | Baseline transition; outcome **values** → Blocked (BD-008) |
| `pending = false` | Any further outcome attempt | Unchanged (409, ACT-SR-03) | Baseline |
| (any) | Action waits on another action | not yet actionable | **Blocked (BD-007)** |
| (any) | Request rejected or cancelled elsewhere | not actionable | **Blocked (BD-008, BD-009)** |

The request-level state model is in TRK §5.

## 6. API Contract
None exposed. ORC is invoked inside SUB-API-01. Its output is observed through TRK-API-01 (`stakeholder_actions`) and ACT-API-01/02.

## 7. Data Requirements
**Stakeholder Action** (owned by this spec): `action_id`, `request_id`, `stakeholder_group` (ORC-SR-07), `pending` (bool), `outcome` (nullable; value set Blocked, BD-008), `outcome_by_user_id` (nullable), `outcome_at` (nullable), `responsible_party` (definition Blocked, BD-001/004..006/017), `created_at`.
Physical model: Plan (TD-005). External reference fields, if BD-019 requires integration: Reserved.

## 8. Security Requirements
SEC-07 and SEC-08 apply. ORC exposes no endpoint. It must not grant visibility: visibility and action rights are evaluated by TRK and ACT under SEC-03/SEC-04.

## 9. Failure Scenarios

| ID | Scenario | Expected handling | Tag |
|---|---|---|---|
| ORC-FS-01 | Creating an action fails after the request row is written | Whole transaction rolled back; SUB returns 500 (SUB-AC-10) | Baseline |
| ORC-FS-02 | Routing policy returns an unknown stakeholder group | Treated as an internal error; transaction rolled back; logged without PII | Baseline |
| ORC-FS-03 | External system unavailable / times out | — | **Blocked (BD-019)**; defined with TD-010 if integration is confirmed |

## 10. Acceptance Criteria

| ID | Criterion | Trace | Tag |
|---|---|---|---|
| ORC-AC-01 | **Given** a routing policy that selects a set of actions, **when** a request is submitted, **then** exactly those actions exist for the request, each with a valid `stakeholder_group`, `pending = true` and no outcome. | ORC-SR-01a, ORC-SR-07 | Baseline (policy is a fixture) |
| ORC-AC-02 | **Given** a failure while creating any stakeholder action, **when** a request is submitted, **then** neither the request nor any of its actions is persisted. | ORC-SR-06 | Baseline |
| ORC-AC-03 | **Given** a routing policy that returns a group outside ORC-SR-07, **when** a request is submitted, **then** the submission fails with 500 and nothing is persisted. | ORC-SR-07, ORC-FS-02 | Baseline |
| — | Actual action set per request, responsible party, sequencing, request-level derivation, integration behaviour | ORC-SR-01b, -02..-05 | **Blocked**. AC to be written on BD confirmation |

## 11. Spec-Derived Test Cases

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| ORC-TC-01 | ORC-AC-01 | API | Fixture policy returns {Manager, HR}; submit | 2 actions; groups match; all pending; outcome null |
| ORC-TC-02 | ORC-AC-01 | API | Fixture policy returns all five groups; submit | 5 actions, one per group. **This is a fixture, not a business rule** |
| ORC-TC-03 | ORC-AC-02 | API | Fixture forces an insert failure on the 2nd action | 500; zero request rows; zero action rows |
| ORC-TC-04 | ORC-AC-03 | Unit | Policy returns `"Legal"` | Exception; transaction rolled back |

## 12. Dependencies
- **Upstream:** SUB.
- **Downstream:** TRK (reads actions), ACT (reads and updates actions).
- **Decisions:** BD-001..008, BD-015, BD-019.

## 13. Traceability
BR-010/BR-011 → ORC-SR-01a, -06, -07 → ORC-AC-01..03 → ORC-TC-01..04. See `.ai-context/traceability.md`.

## 14. Definition of Ready
- [x] Record contract and group vocabulary defined
- [x] Blocked rules explicitly isolated; no Proposed BD option encoded
- [x] Failure scenarios defined
- [ ] BD-001..007 confirmed. **Required before the routing policy can be specified**
- [ ] BD-019 confirmed. **Required before TD-001 / architecture**
- [ ] Gate 1 specification approval
