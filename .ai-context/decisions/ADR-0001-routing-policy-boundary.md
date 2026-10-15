# ADR-0001 — Isolate Stakeholder Routing Behind a Policy Boundary

- **Status:** Proposed / not approved
- **Date:** 2026-10-13
- **Decision owner:** Project Owner
- **Review required:** Feature Specification Gate 1 reviewer and relevant business decision owners
- **Related decisions:** BD-001..BD-008, BD-015, BD-019

## Context
The baseline submission contract requires request creation and creation of the policy-selected stakeholder-action records to be atomic. The actual action set, responsibility, sequence, request-level outcome and execution channel are unresolved business decisions. Hard-coding one proposed option would improperly turn a proposal into a requirement.

## Proposed decision
Keep stakeholder-action selection behind a `RoutingPolicy` boundary. Before the relevant business decisions are confirmed, automated tests may inject a deterministic fixture policy solely to verify record shape and transaction atomicity. The fixture must not be presented as the production business rule.

## Consequences
- Submission persistence and read/action contracts remain decoupled from unresolved routing rules.
- Confirmed routing decisions can be implemented and tested at the policy boundary.
- Production routing is not authorized until the relevant decisions and specification are approved.
- No external integration is implied; BD-019 remains the authority for that choice.

## Approval history
No approval recorded. This ADR remains a proposal pending Gate 1 and business decision closure.
