# Issue Lifecycle

The AIP lifecycle is a strict state machine. Each transition requires updating:

- The `Status:` line in the issue body.
- The `status:` field inside the `aip` schema block.
- A matching label (see label map below).

## States

- `queued`: Ready to be claimed.
- `claimed`: Claimed, waiting to start.
- `in-progress`: Active implementation.
- `review`: Work ready for review.
- `blocked`: Waiting on external input.
- `done`: Acceptance criteria met.
- `canceled`: Task abandoned or superseded.

## Allowed transitions

- `queued -> claimed`
- `claimed -> in-progress`
- `in-progress -> review`
- `review -> done`
- `review -> in-progress` (changes requested)
- `queued -> blocked`
- `claimed -> blocked`
- `in-progress -> blocked`
- `blocked -> queued`
- `any -> canceled`

## Label map

- `aip:queued`
- `aip:claimed`
- `aip:in-progress`
- `aip:review`
- `aip:blocked`
- `aip:done`
- `aip:canceled`

## Claim lease and heartbeat

Claims are time-bound to prevent stale ownership.

- Default lease: 24 hours.
- If no heartbeat is posted before expiry, the issue returns to `queued`.
- Heartbeat comment format:

```
AIP-Heartbeat: <ISO8601 timestamp>
```

The CLI will enforce lease updates once implemented.
