# Project Context — Employee Internal Transfer

## Objective
Provide a single One-Point Employee Portal journey for initiating and tracking an internal transfer.

## Source of Truth
- Business source: `docs/Requirement for SDD.docx`
- Discovery baseline: `.ai-context/BRD.md` (v2.1, accepted as baseline 2026-09-28)
- Business decisions: `.ai-context/decisions/business-decision-register.md`
- Engineering constraints: `.ai-context/constitution.md`
- Feature specification: `.ai-context/specs/spec-slice-map.md` (v2.0) + 4 slices
- Traceability: `.ai-context/traceability.md`
- Reviews: `.ai-context/reviews/`

## Lifecycle
```text
Source Requirement
  -> Discovery / BRD
  -> Business Decision Closure
  -> Spec + AC + API + Tests
  -> Gate 1
  -> Plan / Architecture
  -> Tasks
  -> Test-first RED
  -> Implementation
  -> GREEN / Security
  -> Gate 2
```

## Roles
| Role | Name | Rule |
|---|---|---|
| Project Owner | Ahin Subhra Pradhan | Owns delivery and artefacts |
| Gate 1 Reviewer | Sourav Kumar Maity | Independent peer review |
| Gate 2 Reviewer | TBD | Must be independent from the implementer |

## Important Boundary
The source document does not define manager ownership, conditional routing, sequence, rejection, cancellation, concurrency, detailed date rules, notification channels or external integration. These must be explicitly resolved before workflow-dependent behaviour is baselined.

## Current State
- BRD v2.1 accepted as the business discovery baseline, subject to clarifications (Gate 1 review 2026-09-28). This is not spec approval.
- Feature Specification re-baselined to v2.0: Baseline items are normative; workflow-dependent items are Blocked on business decisions. Awaiting Gate 1 specification review.
- No business decision is Confirmed yet; A-001..003 are not yet validated.
- Plan, tasks, implementation and Gate 2 evidence have not started.
