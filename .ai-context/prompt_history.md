# Claude Prompt — Remediate INT SDD Employee Transfer Project

You are working inside the `employee-internal-transfer` repository.

The repository currently contains a BRD, Constitution, four Specs, status/context files and templates. The major problem is governance: unresolved business decisions have been converted into normative Spec behaviour.

## Objective
Bring the repository back into compliance with the INT SDD lifecycle:

Source Requirement → Discovery/BRD → Business Decision Closure → Constitution → Spec → Gate 1 → Plan → Tasks → Test-first → Implementation → Gate 2.

## Mandatory rules
1. Do not invent business requirements.
2. Do not claim Gate 1 occurred unless an actual reviewer record exists.
3. Treat the supplied `Requirement for SDD.docx` as the source requirement.
4. Preserve traceability from source → BRD → decision → spec → AC → test.
5. Business decisions must live in the BRD/decision register; technical decisions belong in architecture/ADR.
6. Do not turn an unresolved business decision into an API contract, status value, database constraint or acceptance criterion.
7. Do not create implementation tasks before Gate 1 approval.
8. Do not write implementation code before the corresponding test exists and demonstrates RED.
9. Do not assign the Project Owner as the sole Gate 2 approver for their own implementation.
10. Never silently change source requirements to make the implementation easier.

## Files to inspect first
- `.ai-context/source-docs/Requirement for SDD.docx`
- `.ai-context/BRD.md`
- `.ai-context/constitution.md`
- `.ai-context/project_context.md`
- `.ai-context/status.md`
- `.ai-context/specs/*.spec.md`
- `.ai-context/specs/spec-slice-map.md`
- `.ai-context/plans/*`
- `.ai-context/tasks/*`
- `.ai-context/test_cases/*`

## Required remediation

### A. Correct BRD governance
- Change any statement claiming the BRD already passed Gate 1 unless an actual review record proves it.
- Keep the confirmed requirements separate from unresolved decisions.
- Keep business and technical decisions separate.

### B. Create a Business Decision Register
Create `.ai-context/decisions/business-decision-register.md`.
For each unresolved decision record:
- ID
- question
- why it matters
- proposed assessment baseline, if one is needed
- status
- affected specs
- approval/rejection history

Do not present proposed decisions as source facts.

### C. Correct Constitution
The Constitution must contain only engineering constraints such as:
- PHP/MySQL
- PHPUnit
- test-first
- security
- prepared SQL
- authentication
- coverage
- no secrets
- no PII in logs
- datastore constraints

Remove business workflow assumptions such as exact stakeholder routing or sequence.

### D. Re-baseline Specs
Do not preserve these as normative requirements unless the Business Decision Register has been approved:
- exactly five stakeholder tasks for every request
- all tasks created in parallel
- current manager only
- Submitted/Completed status labels
- rejection/cancellation/concurrency rules
- unconditional Payroll/IT/Facilities participation

After decision approval, rewrite each Spec with:
- intent
- scope
- entry/exit conditions
- process flow
- individually identifiable ACs
- API contract
- state changes
- data requirements
- security constraints
- failure handling
- spec-derived tests
- dependencies
- traceability
- Definition of Ready

### E. Gate 1
Create `.ai-context/gate-reviews/gate-1-review.md`.
Gate 1 must be performed by the named reviewer. Record findings, revisions and final decision.

### F. Plan
Only after Gate 1 approval create real plans containing:
- architecture approach
- data model
- integration boundaries
- failure handling
- security
- ADR candidates
- constitution check
- sequencing

### G. Tasks
Only after the Plan is approved create independently verifiable tasks. Each task must map to AC IDs.

### H. Test-first
For every state-changing behaviour:
1. write test;
2. run and capture RED;
3. implement;
4. run and capture GREEN;
5. update traceability.

### I. Final verification
Before claiming completion, verify there is no orphan requirement, no orphan code, no orphan test and no unapproved business behaviour.

## Output
After making changes, provide:
1. files changed;
2. business decisions still open;
3. Gate 1 readiness findings;
4. plan/task readiness;
5. test-first readiness;
6. exact remaining blockers.

Do not start implementation until Gate 1 has actually approved the revised Specs.
