# Project Context — Employee Internal Transfer

## Objective
Provide a single One-Point Employee Portal journey for initiating and tracking an internal transfer.

## Source of Truth
- Business source: `.ai-context/source-docs/Requirement for SDD.docx`
- Discovery baseline: `.ai-context/BRD.md`
- Business decisions: `.ai-context/decisions/business-decision-register.md`
- Engineering constraints: `.ai-context/constitution.md`

## Lifecycle
```text
Source Requirement
  -> Discovery / BRD
  -> Business Decision Closure
  -> Spec + AC + API + Tests
  -> Gate 1
  -> Plan / Architecture
  -> Tasks
  -> Test-first RED
  -> Implementation
  -> GREEN / Security
  -> Gate 2
```

## Roles
| Role | Name | Rule |
|---|---|---|
| Project Owner | Ahin Subhra Pradhan | Owns delivery and artefacts |
| Gate 1 Reviewer | Sourav Kumar Maity | Independent peer review |
| Gate 2 Reviewer | TBD | Must be independent from the implementer |

## Important Boundary
The source document does not define manager ownership, conditional routing, sequence, rejection, cancellation, concurrency, detailed date rules, notification channels or external integration. These must be explicitly resolved before workflow-dependent behaviour is baselined.

## Current State
- Discovery/BRD exists.
- Existing Specs are historical draft material and must not be treated as approved.
- Gate 1 has not occurred.
- Plan, tasks, implementation and Gate 2 evidence are not complete.
