# Workflow: Generate Plan

## Preconditions
- The spec(s) in scope are at least Draft and listed in `specs/spec-slice-map.md`.
- The plan is marked "not implementation-authorized" until Feature Specification Gate 1 is recorded.

## Inputs
`constitution.md`, `architecture.md`, the in-scope `specs/<slug>.spec.md`, `traceability.md`, `decisions/business-decision-register.md`.

## Steps
1. List the Baseline items the plan covers and the Blocked items it excludes, with the blocking BD/A IDs.
2. Describe components and boundaries; record any new technical decision as an ADR.
3. Data model: candidate tables and constraints only for Baseline behaviour. No constraint may encode an open business decision.
4. API / service boundaries: every endpoint with method, path, auth, CSRF and an explicit rate-limit decision ("none, because …" is acceptable).
5. Failure and transaction strategy.
6. Test-first plan: which TCs become which PHPUnit tests, in which order.
7. Gated implementation sequence with dependencies.
8. **Constitution Check:** one row per constitution line with Aligned / Not aligned and evidence. Do not tick a line whose evidence does not exist yet (for example, a secrets scan that has not run).

## Output
`plans/<feature>.plan.md` with version, date and status. Update `status.md` and `architecture.md` the same day.
