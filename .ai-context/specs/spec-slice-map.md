# Feature Specification Index — Employee Internal Transfer (Spec Slice Map)

## Version
v2.0 (2026-09-28): re-baselined against BRD-001 v2.1 after Gate 1 review GATE1-BRD-002-2026-09-28. Supersedes v1.0 of this map and v1.0 of all four slices.

## Status
**Ready for Gate 1 specification review.** Not approved.

## Governance

| Role | Name |
|---|---|
| Project Owner | Ahin Subhra Pradhan |
| Gate 1 Reviewer | Sourav Kumar Maity |
| Gate 2 Reviewer | TBD. Must be independent of the implementer (status.md governance correction) |

## Parent Feature
Employee Internal Transfer Digital Journey (One-Point Employee Portal). BRD: `.ai-context/BRD.md#BRD-001` (v2.1).

The monolithic v1.0 spec and the v1.0 task-management slice are archived in `specs/archive/` (historical, not normative).

---

## 1. How to Read These Specs

### 1.1 Requirement classification
Every spec requirement (SR), acceptance criterion (AC), state transition, API behaviour and test carries one of these tags:

| Tag | Meaning | Implementable? |
|---|---|---|
| **Baseline** | Traceable to a confirmed BRD requirement/rule (BR-xxx, RULE-xxx), a source-derived business authorization rule, or an engineering constraint in `constitution.md` | Yes, after spec Gate 1 approval |
| **Blocked (BD-xxx)** | Depends on an unconfirmed business decision. The **Proposed option** in the register is **not** used as the requirement. | No |
| **Depends on A-00x** | Baseline in intent, but the details depend on an assumption not yet validated (BRD §11.1) | Only after the assumption is validated |
| **Reserved** | A placeholder so the contract has room for a future decision. Nothing is built. | No |

When a decision is confirmed, the owning spec gets a minor version bump, the item changes Blocked → Baseline, and the AC/tests are written. The Change Control rule in `constitution.md` §6 applies.

### 1.2 ID scheme
Spec ID remains the slug. Requirement-level IDs use a short code per slice so the traceability matrix stays readable:

| Code | Spec ID (slug) |
|---|---|
| SUB | `employee-transfer-request-submission` |
| ORC | `employee-transfer-stakeholder-orchestration` |
| TRK | `employee-transfer-status-tracking` |
| ACT | `employee-transfer-stakeholder-action` (renamed from `…-task-management`; see §6) |

Format: `SUB-SR-01` (spec requirement), `SUB-AC-01` (acceptance criterion), `SUB-TC-01` (test case), `SUB-API-01` (endpoint), `SUB-FS-01` (failure scenario). Cross-cutting security requirements are `SEC-01..`. Full chain: `.ai-context/traceability.md`.

---

## 2. Slice Sequence

| Seq | Code | Spec ID | Business outcome | BRD | Depends on | Baseline coverage |
|---|---|---|---|---|---|---|
| 01 | SUB | `employee-transfer-request-submission` | Employee initiates and submits a transfer request | BR-001..007 | — | **High**: capture, optional reason, submit, format/reference validation, duplicate protection. Blocked: eligibility, date rules, concurrency, business validation, status label |
| 02 | ORC | `employee-transfer-stakeholder-orchestration` | The request's stakeholder actions are recorded so progress can be tracked | BR-010 | SUB | **Low**: action record contract, stakeholder groups, atomicity. Blocked: which actions, when, in what order, integration mechanism |
| 03 | TRK | `employee-transfer-status-tracking` | Employee sees status and pending actions in one view | BR-008, BR-009, BR-011 | SUB, ORC | **High**: own-request detail, pending actions, own list, access scoping. Blocked: status value set, completion display, stakeholder view, notifications |
| 04 | ACT | `employee-transfer-stakeholder-action` | A responsible stakeholder records the outcome of their action | BR-010 | ORC | **Low**: state integrity, attribution, requester cannot act. Blocked: who is responsible, outcome values, effect on the request |

```text
BRD-001 v2.1 ──► SUB (01) ──► ORC (02) ──┬──► TRK (03)  read path
                                          └──► ACT (04)  write path ──(runtime data)──► TRK
```

TRK and ACT can be built in either order. Both depend only on the ORC stakeholder-action record contract.

---

## 3. Business Decisions → Blocked Items

| BD | Blocks |
|---|---|
| BD-001, BD-002, BD-003 | ORC-SR-02; ACT-SR-01 |
| BD-003, BD-013 | SUB-SR-07 |
| BD-004, BD-005, BD-006 | ORC-SR-03; ACT-SR-01 |
| BD-007 | ORC-SR-04; ACT-SR-05; request-level transitions (TRK §State Model) |
| BD-008 | ACT-SR-02 (outcome values); ACT-SR-05; ORC-SR-05 |
| BD-009 | SUB-SR-09 (Reserved) |
| BD-010 | SUB-SR-08 |
| BD-011 | SUB-SR-06 |
| BD-012 | TRK-SR-06 (Reserved) |
| BD-014 | Nothing (non-blocking) |
| BD-015 | SUB-SR-05 (status value only); TRK-SR-04; TRK-SR-05; ORC-SR-05 |
| BD-016 | SUB-SR-04 |
| BD-017 | TRK-SR-03b (stakeholder view); ACT-SR-01; confirmation of BAR-001..005 |
| BD-018 | ACT-SR-04b (audit content/retention) |
| BD-019 | ORC-SR-01b (execution mechanism); TD-001 |

---

## 4. Cross-Cutting Security Requirements (technical access control)

These implement the **business authorization rules** in BRD §15.1 using the engineering constraints in `constitution.md` §3. They do not decide who is authorised; they decide how the decision is enforced.

| ID | Requirement | Source | Tag |
|---|---|---|---|
| SEC-01 | Every endpoint requires an authenticated portal session. With no session, the endpoint returns 401 and has no side effects. | Constitution §3; TD-002 | Baseline; mechanism Depends on A-001 |
| SEC-02 | The requesting employee's identity is taken only from the authenticated session. Any `employee_id`/requester field in a payload is ignored and never trusted. | BAR-001 | Baseline |
| SEC-03 | Authorization is deny-by-default. A caller gets access only if a Baseline rule grants it. | Constitution §3 | Baseline |
| SEC-04 | Visibility scoping is applied in the data-access/query layer (the query itself filters by permitted caller), not only in the UI or controller. | Constitution §3; BAR-002, BAR-003 | Baseline |
| SEC-05 | A request or action the caller may not view returns **404 `not_found`**, the same as a non-existent ID, so its existence is not disclosed. | BAR-003 | Baseline |
| SEC-06 | A request or action the caller may view but may not act on returns **403 `forbidden`** and has no side effects. | BAR-005 | Baseline |
| SEC-07 | All SQL uses prepared/parameterised statements. | Constitution §3 | Baseline |
| SEC-08 | Logs and error responses contain no employee PII, stack traces or SQL. Errors return the standard envelope (§5). | Constitution §3 | Baseline |
| SEC-09 | State-changing endpoints enforce the portal's existing CSRF protection. | Constitution §3 (reuse portal auth) | Depends on A-001 |
| SEC-10 | All inputs are validated server-side for type, format and length, whatever the UI validates. | Constitution §3 | Baseline |

---

## 5. API Conventions (all slices)

- Base path follows the project's existing routing convention (Constitution §5). Paths below are shown as `/api/...` and may be re-mapped in the Plan without changing contract semantics.
- JSON request/response; dates `YYYY-MM-DD`; timestamps ISO-8601 with offset.
- **Error envelope** (every non-2xx):
  ```json
  { "error": "string (machine code)", "message": "string (safe, human-readable)", "fields": [ { "field": "string", "code": "string" } ] }
  ```
  `fields` is present only for `validation_error`.
- Standard codes used across slices:

| HTTP | `error` | Meaning |
|---|---|---|
| 400 | `validation_error` | Input fails type/format/length/reference validation |
| 401 | `unauthenticated` | No valid portal session (SEC-01) |
| 403 | `forbidden` | Visible but not permitted to act (SEC-06) |
| 403 | `csrf_invalid` | CSRF check failed (SEC-09) |
| 404 | `not_found` | Does not exist **or** not visible to caller (SEC-05) |
| 409 | `conflict` | State conflict (e.g., action no longer pending); `message` describes it |
| 409 | `idempotency_conflict` | Idempotency key reused with a different payload |
| 500 | `internal_error` | Unexpected failure; no internals disclosed (SEC-08) |

- **Reserved** codes are not implemented until the governing BD is confirmed: `409 active_request_exists` (BD-010), `422 not_eligible` (BD-003/BD-013), `422 effective_date_not_allowed` (BD-011), `422 proposed_value_not_allowed` (BD-016).

---

## 6. Changes from v1.0 (for the Gate 1 reviewer)

| # | Change | Why |
|---|---|---|
| 1 | IDs moved from FR1–FR10 / B1–B10 to BRD v2.1 IDs (BR-xxx, BD-xxx, BAR-xxx) | v1 IDs no longer exist in the BRD |
| 2 | Removed "exactly five tasks, created in parallel" as a normative rule | BD-004..007 are unconfirmed |
| 3 | Removed "current manager only" | BD-001 is unconfirmed |
| 4 | Removed "Submitted"/"Completed" as normative status values; `status` is a field whose value set is Blocked (BD-015) | BRD §13 |
| 5 | "Stakeholder task" → "stakeholder action"; slice 04 renamed `employee-transfer-stakeholder-action` | "Task" implied internal-task orchestration, a removed assumption (BRD §11); integration scope is BD-019 |
| 6 | Non-visible resources return 404 (previously 403) | SEC-05: avoids disclosing that another employee's request exists |
| 7 | Added idempotent submission (SUB-SR-10) as technical failure handling, separate from concurrency policy (BD-010) | Gate 1 finding: duplicate submissions |
| 8 | Gate 2 Reviewer changed from Project Owner to TBD (independent) | status.md governance correction |
| 9 | The v1.0 "UT08 → AC6" traceability correction is carried forward as ACT-AC-02/ACT-TC-02 | Unchanged intent |
