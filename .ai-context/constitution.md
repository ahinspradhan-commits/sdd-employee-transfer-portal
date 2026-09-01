# Project Constitution — Employee Internal Transfer (One-Point Employee Portal)

**Status:** Draft v0.1 — pending your review/approval before Spec drafting begins (constitution changes get the same review rigor as a spec — INT SDD Blueprint §8).

These are the non-negotiables for every feature built in this project. If a plan later violates or is silent on one of these, that's a Gate 1 finding, not a Gate 2 comment. Projects may add stricter rules later; nothing here should be weakened without a recorded amendment.

## Testing Discipline
- Test-first is mandatory for every state-changing operation (request submission, status/state transitions, any stakeholder action) — no exception for "simple" endpoints.
- Backend tests: PHPUnit.
- Minimum coverage floor: 70% line coverage for the transfer-request module. Coverage is a floor, not a target to write to. *(Draft value — adjust if the assignment expects a different bar.)*
- Core workflow/state-transition tests run against a real local MySQL test database (or a transaction that rolls back), not a mocked database — schema and query-level issues must be caught by tests, not discovered later.

## Security Posture
- No PII (employee name, personal contact details, payroll/payment data) appears in logs at any log level, including debug.
- Each stakeholder view (Manager/HR/Payroll/IT/Facilities) must only return requests routed to that stakeholder — enforced at the query/data-access layer, not only hidden in the UI.
- Every state-changing endpoint requires an authenticated session (reusing the Portal's existing login) — no anonymous mutation endpoints.
- All SQL access uses parameterized queries/prepared statements — no string-concatenated SQL, anywhere.
- No secrets or DB credentials committed to the repository, even for the localhost environment — use a config file excluded from version control.

## Architectural Constraints
- Approved datastore: MySQL only (system of record). No new datastore introduced without an ADR.
- Default orchestration model: downstream Manager/HR/Payroll/IT/Facilities steps are tracked as internal task/queue records inside this project's own MySQL schema — not real external system integration — until Decision T1 (BRD.md §20) says otherwise. Changing this later requires an ADR.
- Authentication: the existing One-Point Portal's login is assumed to be session-based and reusable (Assumption A1, BRD.md). No new/parallel auth mechanism is introduced without an ADR — but the exact integration shape (how this feature's endpoints plug into that session) is Technical Decision T4 (BRD.md §20) and remains open, not resolved by this line.
- The Internal Transfer feature's code and tables stay clearly separated from unrelated Portal modules (payroll self-service, IT self-service, etc.) it sits alongside — it's an addition, not a rewrite of the existing portal.

## Non-Functional Baselines
- No formal SLA — this is a localhost training build, single-developer environment. Working default: list/detail pages render in under 1 second locally.
- No availability/RPO/RTO target set (not applicable to a local dev environment).
- *(These are placeholders because the source BRD stated no non-functional requirements — revisit if the assignment expects production-style NFRs.)*

## Versioning Rules
- No public/external API is in scope by default (per the internal-orchestration assumption above). If any endpoint is later exposed for external integration, it must be versioned under a `/api/v1/` prefix and documented with a breaking-change policy at that time.
