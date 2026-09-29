# Spec: Employee Transfer Stakeholder Action

## Spec ID
employee-transfer-stakeholder-action (short code **ACT**). Renamed from `employee-transfer-stakeholder-task-management` (v1.0, archived at `specs/archive/`).

## Status
Draft, ready for Gate 1 specification review. **Portal endpoints Blocked (BD-019)**; outcome invariants are Baseline.

## Version
v2.0 (2026-09-28): re-baselined against BRD-001 v2.1. Supersedes `employee-transfer-stakeholder-task-management` v1.0.

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
`.ai-context/BRD.md#BRD-001` v2.1: BR-010, RULE-006, RULE-007, BAR-004, BAR-005, BAR-006, §4 Actors, BD-001, BD-004..008, BD-017..019

## Parent Feature
See `spec-slice-map.md` v2.0. Slice sequence **04**.

---

## 1. Intent
The outcome of each stakeholder action is recorded exactly once, by the party responsible for it, and is attributable. When it is recorded, the employee's progress view changes (TRK-SR-08).

The source confirms that Manager "confirms", HR "validates", and Payroll/IT/Facilities "may" act (§4). It does not confirm:
- whether stakeholders record outcomes **in the portal** or in their own systems (BD-019);
- who the responsible individual is (BD-001, BD-004..006, BD-017);
- which outcome values exist, e.g. only "done", or also "rejected" (BD-008);
- what an outcome does to the rest of the request (BD-007, BD-008).

This spec therefore defines Baseline **invariants** that hold however BD-019 is decided. The portal endpoints are included as a **draft contract**, Blocked on BD-019.

## 2. Scope

### In scope (Baseline invariants)
- One outcome per action; no overwrite.
- Attribution (who, when) of every outcome.
- The requesting employee cannot record outcomes on their own request.
- Concurrency safety when two recorders race.

### Blocked
- Portal endpoints for stakeholders (BD-019).
- Who is the responsible party (BD-001, BD-004..006, BD-017).
- Outcome value set (BD-008).
- The effect of an outcome on sibling actions and on the request (BD-007, BD-008 → ORC-SR-04/05).
- Audit content and retention (BD-018).

### Out of scope
- Reassignment/delegation of actions. Not in source; would need a new BD.

## 3. Spec Requirements

| ID | Requirement | Trace | Tag |
|---|---|---|---|
| ACT-SR-01 | A stakeholder can see the pending actions for which they are the responsible party. | BR-010, BAR-004 | **Blocked (BD-019 for the portal channel; BD-001, BD-004..006, BD-017 for "responsible")** |
| ACT-SR-02 | The outcome-recording service accepts an outcome only for a pending stakeholder action. Caller authorization and the definition of the responsible party remain decision-gated. | BR-010 | Baseline invariant: only a **pending** action accepts an outcome. **Blocked:** outcome values (BD-008), responsible-party rule (BD-017) and portal channel (BD-019) |
| ACT-SR-03 | An action that is no longer pending rejects any further outcome and stays unchanged. | Integrity (Constitution §4) | Baseline |
| ACT-SR-04a | Every recorded outcome stores the authenticated recorder's user ID and a server timestamp. The recorder identity comes from the session or the integration credential, never from the payload. | BAR-006, SEC-02 | Baseline (technical). BAR-006 confirmation via BD-018 |
| ACT-SR-04b | Audit trail content, history of changes and retention period. | BD-018 | **Blocked (BD-018)** |
| ACT-SR-05 | The effect of an outcome on other actions and on the request's status. | BD-007, BD-008, ORC-SR-04/05 | **Blocked (BD-007, BD-008)** |
| ACT-SR-06 | The requesting employee cannot record an outcome on any action of their own request, even if the routing policy names them as responsible. | BAR-005, SEC-03, SEC-06 | **Blocked (BD-017)** because BAR-005 is not yet confirmed |
| ACT-SR-07 | If two outcomes are submitted concurrently for the same pending action, exactly one is committed. The other receives the ACT-SR-03 conflict. | Integrity | Baseline |

## 4. State Model — Stakeholder Action
Defined in ORC §5. ACT performs the `pending = true → pending = false` transition only.

## 5. API Contract (draft, **Blocked (BD-019)**, not normative)

Included so the reviewer can check how the contract will fit once BD-019 is confirmed as portal-based. Nothing here is built until then.

### ACT-API-01 — GET /api/stakeholder-actions?pending=true
200: `{ "actions": [ { "action_id": 5002, "request_id": 1001, "stakeholder_group": "HR", "pending": true, "created_at": "…" } ] }`. Scope: actions where the caller is the responsible party (Blocked definition). Errors: 401.

### ACT-API-02 — POST /api/stakeholder-actions/{action_id}/outcome
Request: `{ "outcome": "<value set Blocked — BD-008>", "comment": "string, optional, ≤ COMMENT_MAX_LENGTH" }`
200: `{ "action_id": 5002, "pending": false, "outcome": "…", "outcome_at": "…" }`

| HTTP | `error` | Condition |
|---|---|---|
| 400 | `validation_error` | `outcome` missing or not in the value set; comment too long |
| 401 | `unauthenticated` | No session |
| 403 | `forbidden` | Caller can view the action but is not the responsible party (e.g. the requester; ACT-SR-06) |
| 403 | `csrf_invalid` | CSRF failure (SEC-09) |
| 404 | `not_found` | Action does not exist or is not visible to the caller (SEC-05) |
| 409 | `conflict` | Action no longer pending (ACT-SR-03, ACT-SR-07) |

## 6. Data Requirements
Updates the Stakeholder Action entity (ORC §7): `pending`, `outcome`, `outcome_by_user_id`, `outcome_at`, and an optional `comment` (new, nullable). No other entity.

## 7. Security Requirements
SEC-01..SEC-10 apply. Slice-specific:
- The update is conditional in SQL (`… WHERE action_id = :id AND pending = 1`) and checks the affected-row count, which gives ACT-SR-03 and ACT-SR-07 without a read-then-write race.
- The comment is free text and is never logged (SEC-08).

## 8. Failure Scenarios

| ID | Scenario | Expected handling | Tag |
|---|---|---|---|
| ACT-FS-01 | Two recorders race on the same action | One commits; the other gets 409; the stored outcome belongs to the winner | Baseline |
| ACT-FS-02 | Outcome on an action that already has one | 409; unchanged | Baseline |
| ACT-FS-03 | Database failure mid-update | Rolled back; action remains pending; 500 | Baseline |
| ACT-FS-04 | External system reports an outcome for an unknown action | — | **Blocked (BD-019)** |

## 9. Acceptance Criteria

Baseline ACs are verified at **service level** (the outcome-recording service), so they hold whether BD-019 ends with a portal endpoint or an integration adapter as the caller.

| ID | Criterion | Trace | Tag |
|---|---|---|---|
| ACT-AC-01 | **Given** a pending action and a recorder the routing fixture names as responsible, **when** an outcome is recorded, **then** the action becomes `pending = false`, with the outcome, the recorder's user ID and a server timestamp stored. | ACT-SR-02, ACT-SR-04a | Baseline (outcome value is a fixture) |
| ACT-AC-02 | **Given** a pending action, **when** a user who is not the responsible party records an outcome, **then** the action is unchanged and the service reports "not permitted". *(Carries forward the v1.0 UT08→AC6 correction.)* | ACT-SR-02, SEC-03 | Baseline |
| ACT-AC-03 | **Given** an action that already has an outcome, **when** any user records another, **then** it is unchanged and the service reports a conflict. | ACT-SR-03 | Baseline |
| ACT-AC-04 | **Given** the requesting employee is named as responsible by the fixture, **when** they record an outcome on their own request's action, **then** it is refused and unchanged. | ACT-SR-06, BAR-005 | **Blocked (BD-017)**; AC to be finalized when the business authorization rule is confirmed |
| ACT-AC-05 | **Given** two concurrent outcome submissions on one pending action, **when** both execute, **then** exactly one is stored and the other gets a conflict. | ACT-SR-07 | Baseline |
| ACT-AC-06 | **Given** a payload that names a different recorder, **when** an outcome is recorded, **then** the stored recorder is the authenticated caller. | ACT-SR-04a, SEC-02 | Baseline |
| — | Portal list/record endpoints, responsible-party rules, outcome values, downstream effects, audit retention | ACT-SR-01, -04b, -05; ACT-API-01/02 | **Blocked**. AC to be written on BD confirmation |

## 10. Spec-Derived Test Cases

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| ACT-TC-01 | ACT-AC-01 | Service + DB | Fixture responsible user records fixture outcome | Row: pending 0, outcome set, `outcome_by_user_id` = caller, `outcome_at` set |
| ACT-TC-02 | ACT-AC-02 | Service + DB | Unrelated user records outcome | Refused; row unchanged |
| ACT-TC-03 | ACT-AC-03 | Service + DB | Record twice | Second refused with conflict; first outcome retained |
| ACT-TC-04 | ACT-AC-04 | Service + DB | Requester named responsible by fixture | **Deferred until BD-017 confirmation** |
| ACT-TC-05 | ACT-AC-05 | Service + DB | Two DB connections issue the conditional update simultaneously | Exactly one affected row in total; one conflict |
| ACT-TC-06 | ACT-AC-06 | Service + DB | Payload `outcome_by_user_id` = other user | Stored recorder = session user |
| ACT-TC-07 | ACT-AC-01 → TRK-AC-08 | API | Record via service, then GET TRK-API-01 | Action shows `pending: false` |

## 11. Dependencies
- **Upstream:** ORC (Stakeholder Action record, routing policy).
- **Runtime downstream:** TRK.
- **Decisions:** BD-019 (channel), BD-001/004..006/017 (responsibility), BD-008 (outcomes), BD-007 (effects), BD-018 (audit).

## 12. Traceability
BR-010 + BAR-006 → ACT-SR-02, -03, -04a, -07 → ACT-AC-01..03, -05..06 → ACT-TC-01..03, -05..07. ACT-SR-06/ACT-AC-04/ACT-TC-04 remain blocked by BD-017. See `.ai-context/traceability.md`.

## 13. Definition of Ready
- [x] Invariants defined independently of the unresolved channel
- [x] Draft endpoint contract visible but marked non-normative
- [x] Failure and concurrency scenarios defined
- [ ] BD-019 confirmed. **Required before any stakeholder-facing endpoint or integration adapter**
- [ ] BD-008 confirmed. **Required before outcome values**
- [ ] BD-001, BD-004..006, BD-017 confirmed. **Required before the responsible-party rule**
- [ ] Gate 1 specification approval
