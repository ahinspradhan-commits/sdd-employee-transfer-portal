# Traceability Matrix — Employee Internal Transfer

**Version:** v1.0 (2026-09-28) · **Addresses:** Gate 1 follow-up G1R2-FA-03
**Chain:** Source requirement → BRD (BR / RULE / BAR) → Spec Requirement (SR / SEC) → Acceptance Criterion (AC) → Test Case (TC)
**Inputs:** `BRD.md` v2.1 · `decisions/business-decision-register.md` v2.0 · `specs/spec-slice-map.md` v2.0 and the four v2.0 slices

Maintenance rule: any change to a BR, BD, SR, AC or TC updates this file in the same change. Later stages extend each row with `→ Task → Test file → Code`.

---

## 1. Functional Chain (Baseline)

| Source (Requirement for SDD §3) | BR | Spec requirement | Acceptance criteria | Test cases | Blocked remainder |
|---|---|---|---|---|---|
| "initiate an Internal Transfer Request" | BR-001 | SUB-SR-01 | SUB-AC-01, SUB-AC-03 | SUB-TC-01, -04, -05 | — |
| "Select the proposed new department/business unit" | BR-002 | SUB-SR-01, SUB-SR-03 | SUB-AC-01, -03, -04, -05 | SUB-TC-01, -04, -05, -06, -09 | SUB-SR-04 (BD-016) |
| "Select the proposed new location" | BR-003 | SUB-SR-01, SUB-SR-03 | SUB-AC-01, -03, -05 | SUB-TC-01, -04, -09 | SUB-SR-04 (BD-016) |
| "Select the proposed role/job position" | BR-004 | SUB-SR-01, SUB-SR-03 | SUB-AC-01, -03, -05 | SUB-TC-01, -04 | SUB-SR-04 (BD-016) |
| "Provide an effective date" | BR-005 | SUB-SR-01, SUB-SR-03 | SUB-AC-01, -03, -04 | SUB-TC-04, -07 | SUB-SR-06 (BD-011) |
| "Provide an optional reason" | BR-006 | SUB-SR-02, SUB-SR-03 | SUB-AC-01, -02, -04 | SUB-TC-01, -02, -03, -08 | SUB-SR-04 length limit (BD-016) |
| "Submit the request" | BR-007 | SUB-SR-05, SUB-SR-10 | SUB-AC-01, -08, -09, -10 | SUB-TC-01, -12, -13, -14 | SUB-SR-07 (BD-003/013), SUB-SR-08 (BD-010), status value (BD-015), SUB-SR-09 Reserved (BD-009) |
| "View the current status of the request" | BR-008 | TRK-SR-01, TRK-SR-07 | TRK-AC-01, -05, -06 | TRK-TC-01, -07, -08, -11 | TRK-SR-04, -05 (BD-015, BD-007..009) |
| "View actions that are pending with other stakeholders" | BR-009 | TRK-SR-02 | TRK-AC-01, -02, -08 | TRK-TC-01, -02, -03, -10 | — |
| "portal to orchestrate the downstream activities" | BR-010 | ORC-SR-01a, -06, -07; ACT-SR-02, -03, -04a, -06, -07 | ORC-AC-01..03; ACT-AC-01..06 | ORC-TC-01..04; ACT-TC-01..06 | ORC-SR-01b (BD-019), ORC-SR-02 (BD-001..003), ORC-SR-03 (BD-004..006), ORC-SR-04 (BD-007), ORC-SR-05 (BD-008/015), ACT-SR-01 (BD-019, BD-017), ACT-SR-05 (BD-007/008) |
| "provide the employee with a single view of progress" | BR-011 | TRK-SR-07, TRK-SR-08, ORC-SR-01a | TRK-AC-05, -08 | TRK-TC-07, -10, ACT-TC-07 | TRK-SR-05 (BD-015) |

## 2. Authorization Chain (business rule → technical control → AC → TC)

| Business authorization rule (BRD §15.1) | BAR status | Technical control | Acceptance criteria | Test cases |
|---|---|---|---|---|
| BAR-001: initiate only for self | Derived from source | SEC-02 | SUB-AC-07 | SUB-TC-11 |
| BAR-002: view own requests | Derived from source | SEC-04 | TRK-AC-01, TRK-AC-05 | TRK-TC-01, TRK-TC-07 |
| BAR-003: cannot view others' requests | Proposed (BD-017) | SEC-03, SEC-04, SEC-05 | TRK-AC-03 | TRK-TC-04, TRK-TC-05 |
| BAR-004: stakeholder views involved requests | Blocked | SEC-03 (interim deny) | TRK-AC-04 (interim) | TRK-TC-06 |
| BAR-005: only responsible stakeholder records outcome; requester cannot | Proposed (BD-017) | SEC-03, SEC-06, ACT-SR-06 | ACT-AC-02, ACT-AC-04 | ACT-TC-02, ACT-TC-04 |
| BAR-006: outcomes attributable | Proposed (BD-018) | SEC-02, ACT-SR-04a | ACT-AC-01, ACT-AC-06 | ACT-TC-01, ACT-TC-06 |

## 3. Engineering-Constraint Chain (Constitution → SEC/SR → AC → TC)

| Constitution clause | Requirement | Acceptance criteria | Test cases |
|---|---|---|---|
| §3 authenticated session | SEC-01 | SUB-AC-06, TRK-AC-07 | SUB-TC-10, TRK-TC-09 |
| §3 deny-by-default | SEC-03 | TRK-AC-03, TRK-AC-04, ACT-AC-02, ACT-AC-04 | TRK-TC-04, -06; ACT-TC-02, -04 |
| §3 query-layer enforcement | SEC-04 | TRK-AC-03, TRK-AC-05 | TRK-TC-05, TRK-TC-07 |
| §3 prepared statements | SEC-07 | SUB-AC-01 | SUB-TC-16 |
| §3 no PII in logs | SEC-08 | SUB-AC-10, SUB-AC-11 | SUB-TC-14, SUB-TC-15 |
| §3 reuse portal auth (CSRF) | SEC-09 | **None yet.** Depends on A-001 (mechanism unknown) | **None yet** |
| §3 / input validation | SEC-10 | SUB-AC-03, SUB-AC-04, TRK-AC-01 | SUB-TC-04..08, TRK-TC-11 |
| §4 data integrity | ORC-SR-06, ACT-SR-03, ACT-SR-07 | ORC-AC-02, ORC-AC-03, ACT-AC-03, ACT-AC-05 | ORC-TC-03, -04; ACT-TC-03, -05 |
| Failure handling (review finding 7) | SUB-SR-10 | SUB-AC-08, SUB-AC-09 | SUB-TC-12, SUB-TC-13 |

## 4. AC → Test Coverage Check

| Slice | ACs | Tests | Every AC has ≥ 1 test? |
|---|---|---|---|
| SUB | SUB-AC-01..11 (11) | SUB-TC-01..16 (16) | Yes |
| ORC | ORC-AC-01..03 (3) | ORC-TC-01..04 (4) | Yes |
| TRK | TRK-AC-01..08 (8) | TRK-TC-01..11 (11) | Yes |
| ACT | ACT-AC-01..06 (6) | ACT-TC-01..07 (7) | Yes |
| **Total** | **28** | **38** | |

## 5. Business Decision Dependencies (what each open decision holds back)

| BD | Status | Blocked spec items | Release blocker? |
|---|---|---|---|
| BD-001 | Proposed | ORC-SR-02, ACT-SR-01, BAR-004/005 scope | Yes |
| BD-002 | Proposed | ORC-SR-02 | Yes |
| BD-003 | Proposed | ORC-SR-02, SUB-SR-07 | Yes |
| BD-004..006 | Proposed | ORC-SR-03, ACT-SR-01 | Yes |
| BD-007 | Proposed | ORC-SR-04, ACT-SR-05, request transitions | Yes |
| BD-008 | Proposed | ORC-SR-05, ACT-SR-02 outcome values, ACT-SR-05 | Yes |
| BD-009 | Proposed | SUB-SR-09 (Reserved) | Only if confirmed "yes" |
| BD-010 | Proposed | SUB-SR-08 | Yes |
| BD-011 | Proposed | SUB-SR-06 (dev placeholder must not ship) | Yes |
| BD-012 | Proposed | TRK-SR-06 (Reserved) | Only if confirmed beyond in-portal |
| BD-013 | Proposed | SUB-SR-07 | Yes |
| BD-014 | Proposed | — | No |
| BD-015 | Open | SUB-SR-05 status value, TRK-SR-04, TRK-SR-05, ORC-SR-05 | Yes |
| BD-016 | Open | SUB-SR-04 | Yes |
| BD-017 | Open | TRK-SR-03b, ACT-SR-01, BAR confirmations | Yes |
| BD-018 | Open | ACT-SR-04b | Only if confirmed as compliance requirement |
| BD-019 | Open | ORC-SR-01b, ACT-SR-01, ACT-API-01/02, TD-001 | **Yes. Also blocks the Technical Plan** |

## 6. Assumption Dependencies

| Assumption | Spec items depending on it | Blocks |
|---|---|---|
| A-001 portal authentication | SEC-01 mechanism, SEC-09 | Plan; SEC-09 AC/TC |
| A-002 employee organisational data | ORC-SR-02 inputs (once BD-001 closes), SUB-SR-04 (once BD-016 closes) | Plan |
| A-003 master data | SUB-SR-03 reference check, SUB-AC-05, SUB-FS-03/05, TRK-SR-01 names, TRK-FS-01 | Plan; SUB-AC-05 |

## 7. Orphan Check (2026-09-28)

| Check | Result |
|---|---|
| BR with no Baseline SR | None. BR-001..011 each have ≥ 1 |
| Baseline SR with no AC | None |
| AC with no TC | None |
| TC with no AC | None |
| Baseline item tracing to a **Proposed** BD option | None. Proposed options are not used |
| SEC requirement with no AC | **SEC-09** (CSRF). Deferred until A-001 is validated. Tracked here as a known gap |
| Blocked item with no BD | None |
| Code / tasks | Not started (not permitted before Gate 1 approval) |

## 8. Stage Readiness

| Stage | Ready? | Blocking items |
|---|---|---|
| Gate 1 spec review | **Yes**: Baseline scope is reviewable | — |
| Technical Plan | **No** | A-001..003 validation; BD-019 (integration scope, TD-001) |
| Tasks / test-first for Baseline items | After Gate 1 approval + Plan | As above |
| Release of the journey | **No** | Every "Yes" in §5 |
