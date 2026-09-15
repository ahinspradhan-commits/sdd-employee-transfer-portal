# Project Context — Employee Internal Transfer

One-page orientation. What you'd hand a new hire (or a fresh agent session) on day one.

## Objective
Give employees a single digital journey in the One-Point Employee Portal to request and track an internal transfer (department/location/role change), replacing today's fragmented, manually-coordinated process across Manager, HR, Payroll, IT and Facilities. Full detail: `BRD.md#BRD-001`.

## Architecture Summary
- **Portal:** One-Point Employee Portal — an existing web application already offering HR, payroll, IT, learning, and facilities self-service. This feature extends it; it does not stand alone.
- **Stack:** PHP (backend), MySQL (database), web application, localhost development environment.
- **Assumed pre-existing:** Employee login/session, and Employee/Department/Location/Role/Manager reference data (Assumption A1 in BRD.md — not yet confirmed).
- **Not yet decided:** whether downstream Payroll/IT/Facilities orchestration is internal task tracking or real system integration (T1); the request/task state machine (T5); how Department/Location/Role/Manager data is actually sourced (T3). These are Plan/Architecture-stage decisions, not Discovery-stage ones.
- **constitution.md:** does not exist yet in this workspace. Per SDD lifecycle order (Discovery → Constitution → Spec), it should be authored/confirmed before Spec drafting begins.

## Stakeholders / Actors
Employee (requester), Manager (confirms), HR (validates eligibility), Payroll, IT, Facilities. See `BRD.md` §3 for role detail and what's still unresolved about each (e.g., which manager confirms).

## Feature Slug
`employee-internal-transfer` — used for spec/plan/tasks/test-case file naming and branch naming going forward.

## Engagement Context (Training Assignment)
This project is being built as a training/certification exercise in the INT Specification-Driven Development (SDD) methodology, not for a real production stakeholder. Practical implications:
- Deliverables expected: Discovery Analysis → `spec.md` + Acceptance Criteria → Spec-derived test cases → `plan.md` → `tasks.md` → AI prompts log → Security assessment → Gate 1 review → Gate 2 evidence.
- Milestones: M1 Discovery & Specification (Days 1–2), M2 Gate 1 (Day 3), M3 Plan → Tasks (Days 4–5), M4 Implementation & Gate 2 (Days 6–8).
- Recommended timeline: ~8–10 working days, ~30–35 hours total effort.
- Evaluation weighting: SDD traceability 20%, Specification quality 20%, Ambiguity & discovery 15%, Acceptance criteria & testability 15%, Business journey understanding 10%, Task decomposition 10%, Test-first approach 5%, Security & failure handling 5%.
- Because there is no real business sponsor, business-level open questions (BRD.md §20, "Business decisions") will generally need to be resolved by you (acting as both engineer and stakeholder) rather than an external approver — but they must still be resolved explicitly and recorded, not silently assumed.

## Current State
Discovery (Deliverable 1) is complete and captured in `BRD.md`. Spec drafting has happened (4 slices, all Draft — see `specs/spec-slice-map.md`).

> **ACTION REQUIRED — Project Owner (Ahin Subhra Pradhan):** Gate 1 Reviewer (Sourav Kumar Maity) completed a manual Gate 1 review on 2026-09-15. Recommendation: **Rework Required Before Approval** on all 4 active spec slices. Full findings: `reviews/GATE1-BRD-001-2026-09-15.md`. Summary: unresolved business decisions (manager approval ownership, eligibility, Payroll/IT/Facilities triggering, sequencing, rejection, cancellation, concurrent requests) must not be silently encoded as assumptions/spec behavior; add explicit BRD→Spec→AC→Test traceability; define the request lifecycle/state model only once workflow is confirmed; distinguish BRD-stated requirements from discovery-inferred rules; keep external integration scope TBD, not Out of Scope; define API/error contracts once workflow is finalized; convert security considerations into testable requirements. See `status.md` Gate 1 Status column and Daily Execution Log (2026-09-15 entry) for tracking.

## SDD Governance & Accountability

| Role | Name | Responsibility |
|---|---|---|
| Project Owner | Ahin Subhra Pradhan | Overall feature/project delivery accountability |
| Gate 1 Reviewer | Sourav Kumar Maity | Spec and plan/architecture review |
| Gate 2 Reviewer | Ahin Subhra Pradhan | Test, implementation and code review |

> **Governance note:** Project Owner and Gate 2 Reviewer are currently the same person. Per SDD gate independence rules, the Project Owner must not act as the sole Gate 2 approver for their own implementation — an independent Gate 2 reviewer must be assigned before Gate 2 review begins if the Project Owner performs the implementation. See `status.md` for the tracked governance issue.
