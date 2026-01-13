# Agent Process Contract

This contract defines how project manager, tech lead, worker agents, review
agents, and the human maintainer coordinate through GitHub PRs. The goal is to
keep the system simple, CLI-friendly, and safe from duplicate work.

## Single Source of Truth

- PR body contains a single status line:
  - `Agent-Status: ready | in-progress | needs-review | blocked |
    changes-requested | done`

## Status Meanings

- `ready`: Open for a worker agent to claim.
- `in-progress`: Claimed by a worker agent and actively being implemented.
- `needs-review`: Worker complete; review agent (and human) should review.
- `blocked`: Worker needs a decision/input from the human.
- `changes-requested`: Review agent requires changes.
- `done`: Reviewed and approved; awaiting human merge.

## Claim Protocol (Worker + Reviewer)

To avoid two agents working the same PR:

1. Fetch PR and confirm `Agent-Status: ready` (or `needs-review` for reviewers)
   and no assignee.
2. Claim in three steps (as fast as possible):
   - Set `Agent-Status: in-progress`
   - Add self as assignee
   - Comment: `Claimed by <agent-id> at <timestamp>`
3. Re-fetch PR:
   - If assignee or status does not match, back off and stop.

## Local Workspace Isolation

- Use a dedicated git worktree (or separate clone) per PR and per agent run.
- Do not run multiple agents in the same working directory.
- Prefer a consistent path pattern like `../worktrees/<repo>-pr-<num>-<agent>`.

## Worker Agent Responsibilities

- Use TDD: write a failing unit test for each critical path/behavior before
  implementation; document exceptions if test-first is not feasible.
- Ensure unit tests cover critical paths and behavior.
- Implement until unit/integration tests pass.
- Confirm a basic acceptance test is documented early; if missing, request it
  before proceeding.
- Update PR with a brief progress summary and tests run.
- Mark the PR as ready by moving it out of draft and setting
  `Agent-Status: needs-review`, then remove self as assignee.
- Request human review via GitHub review request.

## Review Agent Responsibilities

- Define a basic acceptance test early (when the PR is ready) based on the PM
  spec and acceptance criteria; document it in the PR.
- Validate acceptance criteria and risks.
- Confirm unit/integration tests pass, acceptance tests pass, and the change
  meets the acceptance criteria.
- If fixes are needed, set `Agent-Status: changes-requested` with clear tasks.
- If acceptable, approve and set `Agent-Status: done`.
- Assign the PR to the human for final action.

## Branch Sync After Tooling Fixes

- When a tooling/CI fix PR is merged, the tech lead rebases or updates any
  in-flight PR branches that depended on the failing check, so they pick up
  the fix and return to a green baseline.

## Human Notifications (CLI-first)

- When `Agent-Status: needs-review`, the worker agent must request your review.
- When `Agent-Status: blocked`, the worker agent must @mention you with the
  decision needed.
- If a PR requires human input (questions, approvals, or manual steps), assign
  the PR to the human and state the request clearly in a comment. The human
  will unassign themselves after responding.

## PR Body Template (Required)

```
Agent-Status: ready

## Summary
- ...

## Scope
- ...

## Dependencies
- ...

## Acceptance Criteria
- [ ] ...

## Test Plan
- [ ] Unit/integration tests cover critical paths/behavior (developer, TDD)
- [ ] Basic acceptance test defined early (reviewer/QA)
- [ ] Acceptance tests pass (reviewer/QA)
```
