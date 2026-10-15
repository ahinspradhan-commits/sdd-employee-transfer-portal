# Architecture — Employee Internal Transfer

**Status:** Draft v0.2 (2026-10-13); not approved for implementation. Feature Specification Gate 1, constitution approval, A-001..A-003 validation and relevant business decisions remain outstanding.

## Boundary
The feature extends the existing One-Point Employee Portal. It is not a standalone application.

## Expected Layers
```text
Portal UI
  -> Existing Portal Route / Controller
  -> Application Service
  -> Authorization / Policy
  -> Repository / Data Access
  -> Existing Portal / MySQL
```

## Draft component boundaries
- `RoutingPolicy`: isolates the unresolved stakeholder action set and ordering; fixture-only behavior is permitted in baseline tests. No business routing rule is approved.
- `StakeholderOutcomeService`: contains baseline conditional-update/atomicity invariants; stakeholder eligibility and outcome values remain decision-gated.
- `RequestReadService`: provides requester-scoped detail/list reads with query-level ownership filters.
- `MasterDataProvider`: resolves department/location/role labels after A-003 is validated.

## Data Model Candidates
- `transfer_requests`
- `stakeholder_actions`
- `transfer_status_history` only if an approved decision or reviewed technical design demonstrates it is required.

## Integration Boundary
No external HR/Payroll/IT/Facilities integration is assumed until BD-019 is confirmed. Stakeholder-facing execution endpoints are not approved before then.

## Security / Operations
- Reuse the portal authentication and CSRF conventions after A-001 validation.
- Prepared SQL only; deny-by-default authorization at the query/data-access layer; no employee PII in logs.
- Endpoint rate-limit handling is recorded provisionally in `plans/employee-internal-transfer.plan.md` §5; existing portal configuration must be verified before release.

## ADRs
`decisions/ADR-0001-routing-policy-boundary.md` records the proposed routing-policy boundary as a draft decision. It is not an approved implementation choice until the relevant Gate 1 review and business decisions are complete.

## Gate Rule
No implementation-specific architecture decision is final until the related business behavior is approved and the Feature Specification Gate 1 is recorded.
