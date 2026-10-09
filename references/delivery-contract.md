# Delivery contract

Use this reference for multi-package implementation or work that crosses source, database, services, browser behavior, or deployment.

## Work-package contract

Each package records only fields that affect execution:

| Field | Purpose |
|---|---|
| Outcome | Observable result produced by this package |
| Owner/scope | Existing responsibility and exact affected surfaces |
| Dependencies | Facts or prior packages that must already hold |
| Changes | Ordered implementation actions |
| Invariants | Behavior this package may not change |
| Evidence | Tests or observations mapped to the outcome and risks |
| Failure/stop | What blocks continuation and how to preserve state |
| Delivery state | Planned, implemented, verified, deployed, or complete |

Avoid packages that are merely file groups. Split when outcome, owner, deployment, rollback, or verification differs.

## Canonical planning document contract

The output should match the quality of an implementation handoff, not a brainstorming transcript. For Standard-or-larger work, preserve this reading path when applicable:

1. **Decision snapshot:** outcome, recommendation, status, blockers, and explicit non-goals.
2. **Why this exists:** observed problem, impact, and root-cause evidence.
3. **Current behavior:** actor journey, owner, source of truth, callers, consumers, failure, and recovery.
4. **Target behavior:** concrete before/after behavior and architecture fit.
5. **Decision record:** alternatives, trade-offs, second-opinion result if used, rejected options, and reopen conditions.
6. **Impact map:** source modules, database objects, services, permissions, events, deployment surfaces, and documentation.
7. **Work packages:** dependency order, steps, invariants, evidence, and stop conditions.
8. **Acceptance Map:** each material claim or risk mapped to the lowest sufficient evidence layer.
9. **Migration and recovery:** coexistence rules, cutover, rollback, cleanup, and legacy retirement.
10. **Delivery states:** what counts as local, programmatic, Staging, and Production completion.

Do not create empty sections for a small change. Do not split this reading path into several competing documents merely because each section could be a file.

The canonical plan must be understandable without replaying the conversation. Historical detail belongs only where it explains a current constraint, decision, or anti-pattern.

## Batch-verification contract

Fast local checks protect each coding cycle. Expensive shared verification runs against a coherent candidate, not every commit.

Before the combined hosted run:

- all planned source packages in the batch are implemented;
- focused tests and relevant programmatic gates pass;
- migrations and service changes have deterministic order and readbacks;
- the candidate SHA or artifact is fixed;
- the combined role and journey matrix is written;
- fixtures and cleanup ownership are known.

After the run, fix failures in a bounded batch and rerun the affected matrix plus critical neighboring flows. Do not rerun unrelated journeys only to create activity.

## Status vocabulary

| State | Meaning |
|---|---|
| Proposed | Candidate behavior, not decided |
| Decided | Product or architecture choice is settled |
| Ready | Implementer can start without inventing material decisions |
| Implemented | Source exists; no higher evidence implied |
| Programmatically verified | Named automated checks passed on the stated version |
| Staging verified | Deployed Staging behavior and required readbacks passed |
| Production verified | Production canary or specified real path passed |
| Deferred | Explicitly out of current scope with a reopen condition |
| Blocked | A named missing input or failed gate prevents continuation |
| Superseded | Replaced by a named decision or artifact |

Never compress these states into a percentage.

## Closeout contract

Before marking complete:

1. Reconcile the final diff with every accepted requirement and non-goal.
2. Confirm there is one active implementation path.
3. Record passed, failed, blocked, and unverified evidence separately.
4. Confirm external data or test fixtures were cleaned or explicitly retained.
5. Update current state and remaining Todo without duplicating them.
6. Report the exact delivery boundary reached and the next required action, if any.
