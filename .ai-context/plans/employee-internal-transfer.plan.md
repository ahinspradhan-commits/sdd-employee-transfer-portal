# Technical Plan — Employee Internal Transfer

**Version:** v2.2 (2026-10-13)  
**Status:** Draft / gated — not implementation-authorized. Feature Specification Gate 1 is pending; assumptions A-001..A-003 are open; BD-019 and other workflow decisions remain unconfirmed.

## 1. Source and Governance

| Artifact | Role |
|---|---|
| `BRD.md` v2.1 | Business discovery baseline; accepted subject to clarifications on 2026-09-28, not feature-spec approval |
| `decisions/business-decision-register.md` v2.0 | Authority for business decisions; Proposed/Open items are non-normative |
| `specs/spec-slice-map.md` v2.0 and four slice specs | Feature-spec baseline awaiting Gate 1 review |
| `constitution.md` | Proposed engineering constraints; approval is pending |
| `traceability.md` | BR → SR → AC → TC chain |

| Role | Name |
|---|---|
| Project Owner | Ahin Subhra Pradhan |
| Gate 1 Reviewer | Sourav Kumar Maity |
| Gate 2 Reviewer | Subhajit Mukherjee (independent reviewer named in `reviews/GATE2-EMP-TRANSFER-2026-10-13.md`; nomination to be confirmed by Project Owner) |

## 2. Gate Conditions

This is a planning draft only. No implementation task is authorized until all relevant conditions are met:
1. Feature Specification Gate 1 is approved and recorded in `reviews/`.
2. Constitution is approved and dated by the authorized reviewer/owner; currently Proposed.
3. A-001, A-002 and A-003 are validated with evidence in `BRD.md` §11.1.
4. Named business approvers are recorded and all decisions blocking the in-scope behavior are Confirmed; BD-019 must be closed before stakeholder-facing execution/integration is designed.
5. The Plan Constitution Check below is reviewed again against the approved constitution and approved specs.

BRD Gate 1 acceptance is not Feature Specification Gate 1 approval. This plan does not authorize code or tests to proceed ahead of those gates.

## 3. Baseline Architecture Direction (Draft)

The feature extends the existing One-Point Employee Portal; it is not a standalone application.

```text
Portal UI
  -> Existing Portal Route / Controller
  -> Application Service
  -> Authorization / Policy
  -> Repository / Data Access
  -> Existing Portal / MySQL
```

No new datastore, authentication system or external integration is approved by this plan.

### Replaceable boundaries
- `RoutingPolicy`: isolates ORC routing rules that depend on BD-001..007. Until those decisions are confirmed, tests may use fixture policies only.
- `StakeholderOutcomeService`: records baseline action-outcome invariants; responsibility and outcome values remain decision-gated.
- `RequestReadService`: provides requester-scoped tracking data.
- `MasterDataProvider`: reads department/location/role data only after A-003 validation.

See `architecture.md` and draft `decisions/ADR-0001-routing-policy-boundary.md`. Neither is implementation authorization.

## 4. Data Model — Draft Only

Logical entities derived from current baseline:
- `transfer_requests`
- `stakeholder_actions`
- `transfer_status_history` — not required by the current baseline; introduce only if an approved decision/design requires it.

Physical schema, indexes and foreign keys are not finalized until A-002/A-003 validation and Feature Specification Gate 1 approval. Integrity expectations: requester identity from authenticated session; request and initial actions created atomically; conditional outcome update only while pending; visibility filters enforced at query/data-access layer; no employee PII in logs.

## 5. API / Service Boundaries

Baseline endpoints in the specs:
- `POST /api/transfer-requests` — SUB (state-changing; CSRF applies once A-001 is validated).
- `GET /api/transfer-requests/{request_id}` — TRK (read-only).
- `GET /api/transfer-requests` — TRK (read-only).
- Stakeholder-facing ACT endpoints are blocked by BD-019. ORC exposes no public endpoint.

### Endpoint rate-limit decisions (provisional technical plan; verify existing portal configuration during A-001)

| Endpoint | Decision | Rationale / release condition |
|---|---|---|
| `POST /api/transfer-requests` | Reuse existing portal/session-level throttling if present; do not introduce a bespoke limiter in this assessment draft. | Avoid duplicating portal controls. If no effective throttling exists, record the risk and obtain security review before release; this is not a claim that a limiter currently exists. |
| `GET /api/transfer-requests/{request_id}` | Reuse existing portal/API throttling if present; no feature-specific limiter proposed in this draft. | Read-only endpoint with owner-scoped query; verify existing configuration before release. |
| `GET /api/transfer-requests` | Reuse existing portal/API throttling if present; no feature-specific limiter proposed in this draft. | Read-only list endpoint; verify pagination and existing configuration before release. |
| Stakeholder-facing ACT endpoints | Not approved / not defined until BD-019 and access decisions are confirmed. | Rate-limit decision must be revisited when an endpoint is actually specified; no endpoint may ship without an explicit decision. |

These are draft decisions, not evidence that the existing portal already has throttling. A-001 validation must document actual configuration.

Cross-cutting conventions: JSON responses; ISO-8601 timestamps with offset; standard error envelope; errors per slice specs; CSRF convention remains dependent on A-001 validation.

## 6. Failure / Transaction Strategy

Baseline: validate before persistence; create request and initial actions in one transaction; roll back the unit on persistence/routing failure; support idempotency for repeated submissions carrying the same `Idempotency-Key`; use conditional SQL update for concurrent outcomes; return safe error envelopes without SQL, stack traces or PII. Workflow-specific rejection/cancellation/integration failure behavior remains blocked by BD-007..009 and BD-019.

## 7. Security

- Reuse existing portal authentication; no parallel auth.
- Resolve requester identity only from the authenticated session.
- Deny by default; enforce visibility in the query/data-access layer.
- Non-visible resources use 404 to avoid existence disclosure.
- Use prepared/parameterized SQL and server-side validation.
- State-changing endpoints must enforce the existing portal CSRF protection after A-001 is validated (SEC-09-AC-01 / SEC-09-TC-01).
- Do not log employee PII or free-text reason values.
- Confirm endpoint rate-limit configuration before release; see §5.

## 8. Test-First Plan

The spec test inventory is copied to `test_cases/<slug>.test_cases.md` and is authoritative after Gate 1 revisions:
- SUB: `SUB-TC-01..16` plus cross-cutting `SEC-09-TC-01`
- ORC: `ORC-TC-01..04`
- TRK: `TRK-TC-01..11`
- ACT: `ACT-TC-01..07`; `ACT-TC-04` remains deferred until BD-017 confirmation
- Cross-feature cases: `test_cases/_integration.md`

Sequence after Gate 1: test/database fixture foundation → write and run RED tests for the first authorized task → implement to GREEN → refactor/security → integration/regression → coverage and evidence. No RED/GREEN evidence exists yet.

## 9. Gated Implementation Sequence

| Phase | Work | Gate / status |
|---|---|---|
| P0 | Validate A-001..A-003; nominate business approvers | **Blocked / open** |
| P1 | Approve four slice specs and traceability at Feature Gate 1; approve constitution | **Pending reviewer action** |
| P2 | Finalize physical model and routes from approved specs | After P0/P1 |
| P3 | SUB baseline implementation, tests first | After P1; pending |
| P4 | ORC baseline atomic record implementation using fixture policy | After P1; pending |
| P5 | TRK requester-scoped reads | After P1; pending |
| P6 | ACT baseline service invariants | After P1; stakeholder endpoint remains gated |
| P7 | Confirm business decisions and revise affected specs/tests | As approvals arrive |
| P8 | Security, integration, coverage, traceability, independent Gate 2 review | After implementation and evidence |

## 10. Explicitly Deferred

Do not implement or encode as business rules until confirmed: manager ownership/mandatory step (BD-001/002); HR eligibility (BD-003/013); Payroll/IT/Facilities triggers (BD-004..006); sequencing (BD-007); rejection/cancellation (BD-008/009); active-request concurrency (BD-010); effective-date restrictions (BD-011); notifications (BD-012); status/completion (BD-015); proposed-value business validation (BD-016); stakeholder visibility/responsibility (BD-017); audit content/retention (BD-018); external vs portal execution (BD-019).

## 11. Constitution Check (reviewed against current Proposed constitution; re-check after approval)

| Constitution constraint | Check | Evidence / limitation |
|---|---|---|
| PHP backend | [x] Aligned | Proposed constitution §1 specifies PHP; no implementation code reviewed. |
| MySQL only; no extra datastore without ADR | [x] Aligned | Proposed constitution §4 and plan §3/§4 use existing portal MySQL; physical design remains draft. |
| PHPUnit | [x] Aligned | Proposed constitution §2; test foundation is planned, not present. |
| Test-first for state changes; RED before implementation | [x] Aligned as process | Constitution §2 and plan §8; no RED evidence exists and no code may start before gates. |
| 70% line-coverage floor | [x] Recorded | Proposed constitution §2 sets 70%; rationale recorded in `constitution.md` §2. No measured coverage exists. |
| No secrets committed | [ ] Rule in place; not yet evidenced | `.gitignore`, `.agent/rules/.agentignore` and `.agent/rules/int-standards.md` §5 added 2026-10-15. Only a keyword grep of the working tree has been run (no hits). A proper secrets scan of the working tree and history is required before this line can be ticked. |
| Authorization enforced at data layer | [x] Aligned | Plan §3/§7 and TRK specs require query-level owner filtering; not yet implemented or tested. |
| Existing portal authentication reused | [x] Aligned, conditional | Plan §3/§7; A-001 remains open, so actual integration is not verified. |

**Check result:** draft plan is directionally aligned with the proposed constitution, but this is not constitution approval, spec approval, or implementation authorization. Re-run after Gate 1 and assumption validation.
