# Auto Log — Recording Rules

Agents keep a factual trail of what they did so reviewers can verify it against the repository.

## What to record
| Event | Where | Content |
|---|---|---|
| Prompt that produced or changed an artefact | `.ai-context/prompt_history.md` | Date, task/finding ID, prompt summary, files changed |
| Test RED / GREEN | Task row in `tasks/<slug>.tasks.md` | Command, date, pass/fail count, short failure reason for RED |
| Artefact status change | `.ai-context/status.md` (same day) | New status, evidence path |
| Technical design decision | `.ai-context/decisions/ADR-NNNN-<title>.md` | Context, decision, consequences |
| Business decision change | `decisions/business-decision-register.md` first, then specs | Decision ID, approver, date |
| Review remediation | `reviews/<GATE>-REMEDIATION-<date>.md` | Per finding: what changed **in the repository**, with commit hash |

## Rules
- Log only what actually happened in this repository. Work done on a copy, zip or other workspace is not remediation until it is committed here.
- Cite a commit hash or file path as evidence for every "Fixed" claim.
- Never log secrets, credentials or employee PII.
- Dates are absolute (YYYY-MM-DD).
