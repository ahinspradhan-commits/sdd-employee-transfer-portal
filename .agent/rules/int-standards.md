# INT Standards — Always-On Rules (PHP / MySQL)

These rules apply to every agent action in this repository. `.ai-context/constitution.md` is the source of the engineering constraints; if the two disagree, the constitution wins and this file must be corrected.

## 1. Gate discipline
- Do not write code for a task unless its spec has a recorded Feature Specification Gate 1 approval in `.ai-context/reviews/` and the task is not `Pending`/`Blocked` in its `tasks/<slug>.tasks.md`.
- Never turn a `Blocked` item or an unresolved business decision (BD-xxx) into code, a status value, a DB constraint or an API contract.
- Never claim an approval, a test run, a scan or a review that did not happen.
- Refer to work by identity: `<slug>.T##`, `<AC-ID>`, `<TC-ID>`.

## 2. Test-first
- For every state-changing behaviour: write the PHPUnit test first, run it, record the RED output, then implement, then record GREEN.
- Record RED/GREEN evidence in the task row (command, date, result) before marking the task Done.
- Workflow/state-transition tests run against a real local MySQL test database or a rollback-isolated transaction.
- Coverage floor: 70% line coverage for the transfer module.

## 3. PHP
- `declare(strict_types=1);` in every PHP file. Type-declare parameters, return types and properties.
- PSR-12 formatting, PSR-4 autoloading. One class per file.
- Layering: route/controller → application service → policy/authorization → repository. Controllers hold no SQL and no business rules.
- No `eval`, no `extract`, no dynamic includes from request data.
- Escape all output for its context (`htmlspecialchars(..., ENT_QUOTES, 'UTF-8')` for HTML).

## 4. MySQL / data access
- PDO with prepared statements only; `PDO::ATTR_EMULATE_PREPARES = false`, `ERRMODE_EXCEPTION`. No string-built SQL with user input.
- Ownership/access filters belong in the query (`WHERE requester_id = :uid`), not only in the UI.
- Multi-row state changes run in a transaction; concurrent updates use conditional updates (`WHERE status = :expected`) and check affected rows.
- Schema changes go in versioned migration files; no ad-hoc DDL.

## 5. Security
- Reuse portal authentication and CSRF protection; no parallel auth mechanism.
- Every state-changing endpoint: authenticated session + CSRF token + deny-by-default authorization.
- Every new endpoint has an explicit rate-limit decision in the plan (including "none, because …").
- No secrets, credentials or `.env` files committed. Configuration comes from environment or untracked local config.
- No employee PII in logs (no names, emails, employee IDs in free text; log request IDs instead).
- Validate all input server-side against the spec's validation rules.

## 6. Commits and PRs
- Small commits, one task per commit where possible; message references the task ID.
- No AI-attribution text in code comments.
- Never commit archives (`*.zip`), backups (`*_bkp_*`) or personal working files. Use branches, PRs or tags to hand over work.
- Update `status.md` and the affected spec/task status on the same day as the change.
