# Issue Body Format

Every task must include a single `Status:` line and one `aip` schema block.
Agents should ignore any content outside the block unless explicitly told.

## Required header

```
Status: queued
Schema: AIP-0.1
```

## Required schema block

```aip
version: "0.1"
status: queued
owner: null
goal: "Short, testable outcome."
acceptance_criteria:
  - "Clear, testable condition."
acceptance_tests:
  - "Describe the acceptance test or manual check."
inputs:
  - "Inputs or sources needed."
outputs:
  - "Artifacts or results produced."
dependencies: []
risks: []
constraints:
  - "Known constraints."
non_goals:
  - "Explicitly out of scope."
lease:
  claimed_by: null
  claimed_at: null
  expires_at: null
metadata:
  created_by: null
  created_at: null
  updated_at: null
notes: ""
```

## Notes

- `Status:` and `status:` must match.
- `acceptance_tests` is required before work starts.
- `lease` fields are updated by the claiming agent.
