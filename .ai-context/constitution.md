# Project Constitution — Employee Internal Transfer

**Status:** Proposed v1.0 — approve before Gate 1.

The Constitution contains engineering constraints. It must not silently decide business workflow behaviour that belongs in the BRD/business-decision register.

## 1. Technology
- Backend: PHP.
- Database: MySQL.
- Development environment: localhost/XAMPP.
- Automated tests: PHPUnit.
- Existing One-Point Portal authentication is reused; no parallel authentication mechanism is introduced.

## 2. Testing Discipline
- Test-first is mandatory for every state-changing operation.
- A corresponding automated test must exist and demonstrate RED before implementation of that behaviour.
- Core workflow/state-transition tests use a real local MySQL test database or an equivalent rollback-isolated database transaction.
- Minimum coverage floor: 70% line coverage for the transfer module.

## 3. Security
- Every state-changing endpoint requires an authenticated session.
- Request/task access must be enforced at the data-access/query layer, not only in the UI.
- SQL uses prepared/parameterized statements only.
- No secrets or credentials are committed.
- No employee PII appears in logs.
- Authorization is deny-by-default.

## 4. Architecture
- MySQL is the only system-of-record datastore for this assessment.
- No additional datastore is introduced without an ADR.
- External HR/Payroll/IT/Facilities integration is not technically assumed. Whether the business requires such integration is governed by the BRD and Business Decision Register.
- Internal task records may be used only after the business workflow is approved.
- Feature code and tables remain isolated from unrelated portal modules.

## 5. API
- Internal application endpoints may use the project's existing routing convention.
- If a public/external API is introduced, it must be versioned and documented.

## 6. Change Control
- A business decision changes the BRD/decision register first, then affected specs.
- A technical design decision is recorded as an ADR.
- No implementation task may begin until the relevant spec has passed Gate 1.
