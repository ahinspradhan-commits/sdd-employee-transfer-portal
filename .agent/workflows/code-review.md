# Workflow: Code Review (Gate 2 preparation)

Run before requesting Gate 2 and on every PR. The independent Gate 2 reviewer performs the formal review; this workflow prepares the evidence.

## Entry check (stop if any fails)
1. The spec for every task in the diff has a recorded Feature Specification Gate 1 approval in `.ai-context/reviews/`.
2. The constitution is approved and dated.
3. Each task in the diff has RED then GREEN evidence recorded.
4. The Gate 2 reviewer is named in `status.md` and confirmed by the Project Owner.

## Steps
1. **AC by ID.** For each AC claimed by the diff, name the test(s) that verify it and the code that satisfies it. Mark unverified ACs explicitly.
2. **Traceability.** Confirm `traceability.md` maps BR → SR → AC → TC → test file for the changed scope; no orphans.
3. **Security checklist** (Blueprint §30):
   - Prepared statements only; no string-built SQL.
   - Authenticated session, CSRF token and deny-by-default authorization on every state-changing endpoint.
   - Access filters enforced in the query layer.
   - Rate-limit decision recorded per new endpoint.
   - Output escaping; server-side input validation.
   - No secrets committed; no PII in logs.
4. **Scans.** Attach SAST and dependency-scan output (for example `composer audit`, a PHP static analyser) and a secrets scan of the diff.
5. **Tests and coverage.** Run PHPUnit; attach pass count and module line coverage (floor 70%).
6. **Architecture.** If the diff adds a component, table or boundary, confirm `architecture.md` and an ADR are updated.
7. **Readability.** Naming, duplication, layering per `.agent/rules/int-standards.md`.
8. **Hygiene.** No archives, backups, personal files or AI-attribution in the diff.
9. **Status.** `status.md` and spec/task status updated the same day.

## Output
A findings table (ID, severity Blocker/Major/Minor, file:line, required action) and a checklist table with Pass / Fail / Not verifiable and evidence for each line.
