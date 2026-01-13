# Agent Issue Protocol (AIP)

AIP turns GitHub Issues into a strict, machine-readable task queue for agents.
It defines a schema, a lifecycle, and a claim/lease protocol so machines can
coordinate work deterministically using GitHub as the system of record.

## Why AIP

- Machines need unambiguous inputs, outputs, and acceptance criteria.
- Human-friendly issue text is too loose for automation.
- GitHub is already the shared task surface for many teams.

## What is included

- Issue schema v0.1 in `schema/issue.schema.json`.
- Issue body format and lifecycle docs in `docs/`.
- Issue and PR templates aligned with `agent-process-contract.md`.
- A plan to add a CLI for create/claim/validate/complete (Milestones M1-M3).

## Process expectations

- Every task uses the AIP issue format with a `Status:` line and schema block.
- Status is mirrored by labels and updated on each transition.
- Claims are leases that expire unless a heartbeat is posted.
- PRs follow `agent-process-contract.md` and reference an AIP issue.
- Branch naming: `issue/<number>-<short-slug>`.

## Status lifecycle (v0.1)

`queued -> claimed -> in-progress -> review -> done`

Other transitions:

- `queued` or `in-progress` -> `blocked`
- `blocked` -> `queued`
- `review` -> `in-progress` (changes requested)
- any -> `canceled`

See `docs/issue-lifecycle.md` for details.

## No paid dependencies

This repo is designed to run on GitHub free tier with no paid services.
If a future change requires paid infrastructure, it must include a clear
funding plan and explicit approval.

## Repo layout

- `schema/issue.schema.json`: JSON Schema for the AIP issue block.
- `docs/issue-format.md`: Issue body requirements and example.
- `docs/issue-lifecycle.md`: Status transitions, labels, lease rules.
- `.github/ISSUE_TEMPLATE/agent-task.md`: Issue template for agents.
- `.github/pull_request_template.md`: PR template with Agent-Status line.
- `agent-process-contract.md`: PR status and claim protocol.

## License

Apache-2.0
