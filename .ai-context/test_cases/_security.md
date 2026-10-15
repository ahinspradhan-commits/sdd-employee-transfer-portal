# Cross-cutting Security Test Cases

**Status:** Draft inventory; execution depends on A-001 validation and Feature Specification Gate 1.

| Test ID | AC | Level | Scenario | Expected |
|---|---|---|---|---|
| SEC-09-TC-01 | SEC-09-AC-01 | Integration / security | Submit a state-changing transfer request with missing or invalid CSRF token; inspect persistence. Repeat with a valid token. | Invalid token returns safe 403 `csrf_invalid` and no state changes; valid-token control confirms portal CSRF convention is active. |
