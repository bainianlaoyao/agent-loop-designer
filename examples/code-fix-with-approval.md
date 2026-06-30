<!--
EXAMPLE — full loop.
Shows: all six sections, a multi-domain task with an irreversible action.
Demonstrates Approval Gates (before the irreversible write), Residual Routing
(LOCAL / PLANNER / HUMAN), and a Progress Invariant that converges on
failing-tests-count. This is what a high-risk loop looks like at full size.
-->

# Loop: Fix the failing auth test suite and apply the migration

## Goal & Success Signal

**Goal:** Make the authentication test suite pass, then apply the
accompanying database migration.

**Success signal:** `tests/auth/` passes with zero failures AND the migration
has been applied AND a post-migration smoke check confirms the auth flow works
end to end. All three required; "tests look fixed" is not a success signal.

## Termination Conditions

Stop when any one holds:

- **Goal achieved** — the success signal is satisfied.
- **Max iterations** — 10 fix-iterations.
- **No progress** — `failing_tests` does not decrease for 3 consecutive
  iterations.
- **Escalation** — a residual is classified HUMAN (see Residual Routing).

## Progress Invariant

The bounded quantities each path must advance:

```
failing_tests: N         # must trend toward 0
migration_applied: bool
smoke_check_passed: bool
```

Every loop path must decrease `failing_tests`, or flip a boolean toward done,
or terminate. A path that does none is unbounded and is not allowed — if a fix
does not reduce failures, the hypothesis must change, not the same edit
repeat.

## Residual Routing

- **LOCAL** — a fix did not work; form a new hypothesis and retry within the
  loop (subject to the no-progress bound).
- **PLANNER** — the failures reveal the goal was mis-scoped (e.g. the tests
  encode a requirement the task did not mention). Return to Goal and re-scope.
- **HUMAN** — the migration is irreversible-destructive in a way the task did
  not authorize, or test intent is genuinely ambiguous. Escalate.

## Approval Gates

**Gate: before applying the migration.**

- **Gated action:** running the database migration (irreversible on real data).
- **Entry condition:** the test suite passes — do not request approval until
  the cheaper, reversible work is done and the migration is actually needed.
- **Grant:** apply the migration, then run the smoke check.
- **Deny:** stop with tests-passing-but-migration-skipped, report the state,
  do not apply.

No other action is gated; source edits and test runs proceed freely.

## Measurement Domain

- **Code domain** (the fix) — verified by running `tests/auth/`. Source
  inspection alone does not count.
- **Data/migration domain** — verified by the migration tool's own success
  status AND a post-migration smoke check that exercises the auth flow.
  Inspecting the migration script is not verification of its effect.
