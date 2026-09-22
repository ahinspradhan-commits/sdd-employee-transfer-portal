# Architecture — Employee Internal Transfer

**Status:** Not Started — architecture must be baselined after Gate 1.

## Boundary
The feature extends the existing One-Point Employee Portal. It is not a standalone application.

## Expected Layers
```text
Portal UI
  -> Controller / Route
  -> Application Service
  -> Authorization / Policy
  -> Repository / Data Access
  -> MySQL
```

## Data Model Candidates
- transfer_requests
- stakeholder_tasks
- transfer_status_history (only if approved as necessary)

## Integration Boundary
No external HR/Payroll/IT/Facilities integration is assumed until the business decision is closed.

## ADRs Required If
- a new datastore is introduced;
- external integration is introduced;
- authentication architecture changes;
- workflow state model materially changes the approved business journey.

## Gate 1 Rule
No implementation-specific architecture decision is final until the related business behaviour is approved.
