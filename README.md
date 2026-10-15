# Employee Internal Transfer

A feature for the One-Point Employee Portal that lets an employee start and track an internal transfer request. It is built with the INT Spec-Driven Development (SDD) lifecycle.

## Current state (2026-10-15)

**There is no application code yet.** The repository holds the SDD artefacts only. Implementation is gated:

- Feature Specification Gate 1: pending review
- Constitution: proposed, approval pending
- Assumptions A-001..A-003 and business decision BD-019: open
- Gate 2: Changes Requested / Not Ready

See [.ai-context/status.md](.ai-context/status.md) for the live status.

## Repository layout

| Path | Contents |
|---|---|
| `docs/` | Source requirement (`Requirement for SDD.docx`) and the INT SDD Blueprint |
| `.ai-context/BRD.md` | Business requirements (discovery baseline) |
| `.ai-context/constitution.md` | Engineering constraints |
| `.ai-context/specs/` | Spec slice map and four slice specs (`archive/` holds superseded versions) |
| `.ai-context/plans/`, `tasks/`, `test_cases/` | Plan, per-slice tasks (`<slug>.T##`) and test-case inventories |
| `.ai-context/decisions/` | Business decision register and ADRs |
| `.ai-context/reviews/` | Gate review records and remediation records |
| `.ai-context/traceability.md` | BR → SR → AC → TC matrix |
| `.agent/` | Always-on agent rules and workflows |

## Setup (planned stack)

- PHP 8.x and MySQL via XAMPP (localhost)
- Composer, PHPUnit
- Reuses the existing One-Point Portal authentication

1. Clone into the XAMPP web root, for example `C:\xampp\htdocs\employee-internal-transfer`.
2. Start Apache and MySQL from the XAMPP control panel.
3. Once `composer.json` exists: `composer install`.
4. Configuration and credentials go in an untracked local file (`.env`); never commit them.

## Run

Not runnable yet. Run and URL instructions will be added with the first implemented task.

## Test

Planned command once `tests/` exists:

```sh
vendor/bin/phpunit
```

Tests are written first and must show RED before implementation (see [.agent/workflows/generate-tests.md](.agent/workflows/generate-tests.md)).

## Deploy

Not defined. The feature deploys as part of the One-Point Portal. Deployment steps will be added after Gate 2.

## Contributing

- Follow [.agent/rules/int-standards.md](.agent/rules/int-standards.md).
- Share work by branch, PR or tag. Do not commit archives, backup copies or personal files (see `.gitignore`).
