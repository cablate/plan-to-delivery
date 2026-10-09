---
name: plan-to-delivery
description: Turn ambiguous product or engineering change requests into current-state-grounded, executable plans, then carry authorized work through implementation, verification, documentation, and delivery. Use for multi-step features, bug clusters, refactors, migrations, architecture changes, or resumed work where scope, owners, risks, evidence, and completion state must stay aligned. Do not use for a trivial isolated edit or a purely exploratory question that does not need a plan.
---

# Plan to Delivery

Build one trustworthy chain from **intent → current facts → decisions → work → evidence → final state**. The user should review product choices and material trade-offs, not repeatedly remind the agent how to investigate, plan, test, or close the work.

This skill owns end-to-end orchestration and works on its own. If a formal planning or second-opinion skill is installed, it may produce the plan or review a decision; do not duplicate its schemas. Use testing, security, UI, database, release, or document skills only when their concern is actually triggered.

## Plan size

Small, reversible changes need no written plan: do them and report. A single-owner, reversible fix may need only a short behavior contract, a focused check, the implementation, and final verification. For large, risky, or multi-owner work, write one plan (outcome, scope, non-goals, verification, rollback) and get the user's confirmation before building. No external planning or second-opinion tool is required; if one is used, record its result next to the decision it informed.

## Select the operating mode

| Mode | Use when | Primary output |
|---|---|---|
| Reconstruct | Requirements, documents, code, and prior decisions disagree | Verified problem statement and decision ledger |
| Plan | The user wants an implementation-ready proposal | One canonical executable plan |
| Execute | The plan is ready and implementation is authorized | Code, evidence, status updates, and reviewable commits |
| Resume | Work already exists across branches, PRs, or environments | Freshness check, remaining-work map, and continued execution |
| Repair | A plan or implementation drifted, failed, or expanded incorrectly | Root-cause correction in the same canonical artifacts |


## Manage progress with a delivery state machine

For work that spans more than one meaningful phase, keep one Delivery Ledger inside the canonical plan or status artifact. It records the current stage, freshness anchor, active units, evidence, blockers, next executable action, and recovery point. Do not rely on conversation memory or percentages.

The default stages are:

```text
S0 Align intent
→ S1 Reconstruct current state
→ S2 Resolve decisions
→ S3 Plan Ready
→ S4 Implement the agreed batch
→ S5 Programmatically verify
→ S6 Verify the deployed candidate
→ S7 Release Ready
→ S8 Verify Production
→ S9 Reconcile documents and close
```

Small changes may collapse adjacent stages, but may not borrow evidence or skip an applicable risk. A stage advances only when its exit evidence exists. New evidence may move the task backward to the specific invalidated stage; do not restart everything or silently continue from a stale decision.

Every work package, investigation, migration, or acceptance journey that can progress independently is a **delivery unit**. Each unit owns its local state, inputs, output, evidence, blocker, next action, and recovery point. The top-level ledger summarizes units without copying their details.

At the beginning of every resumed turn:

1. Locate the canonical ledger and current stage.
2. Recheck the repository, environment, and version freshness anchor.
3. Reconcile running, completed, failed, and externally changed units.
4. Select the next dependency-ready unit instead of reopening settled work.
5. Update the ledger whenever a transition, blocker, decision, or evidence result changes.

Read [references/state-machine.md](references/state-machine.md) for stage gates, unit schema, and transition rules.

## Load an optional project profile

The core workflow is project-neutral. A Project Profile may add project-specific navigation, owners, environments, invariants, tools, verification journeys, release conventions, and user preferences. It may not weaken this skill's hard gates, claim evidence, override higher instructions, or grant external mutation.

Profiles may be layered as **generic core → optional user-preference overlay → optional project profile → current task decisions**. Current explicit instructions and governing repository rules still take precedence. Load a project profile only when the current workspace matches its declared repository markers or the user explicitly selects it. Prefer a maintained project-local profile when one exists; a bundled profile is a fallback or example. Verify profile facts against current source and environment when they affect execution.

Read [references/project-profile-contract.md](references/project-profile-contract.md) when creating, selecting, or maintaining a profile. Copy [references/profile-template.md](references/profile-template.md) when a user or project needs a new overlay. For the Synora interactive-engine repository, load [references/project-profiles/synora.md](references/project-profiles/synora.md) after confirming the workspace markers.

## 1. Establish the change contract

Before expanding the work, state:

- the user-visible or operator-visible outcome;
- the triggering problem and why it matters now;
- actors and environments affected;
- behaviors that must remain unchanged;
- explicit non-goals and authorization boundaries;
- the evidence required to call the result complete.

Turn unclear language into observable before/after behavior. Continue independent evidence gathering while optional preferences remain open. Ask the user only when a missing product decision, external authorization, or irreversible choice would materially change the result.

## 2. Reconstruct the truth before designing

Fix a freshness anchor: repository, branch, commit, dirty state, deployed target, database or service environment, and evidence date. Then inspect only the sources needed to answer the change:

1. latest explicit user decisions;
2. current source, schema, configuration, tests, and runtime behavior;
3. current canonical documentation;
4. historical plans, reports, conversations, commits, and screenshots as leads or rationale.

Do not promote history into current fact. Label material claims as **Verified**, **Inference**, **Unknown**, **Decision**, or **Proposal** until resolved. When sources disagree, record which source owns which claim and run the smallest check that can resolve the conflict.

For complex behavior, build a compact map:

```text
actor → entry point → owner → write/source of truth
      → notification/cache → consumer/readback
      → visible result → failure/recovery
```

Trace sibling callers only when a shared owner, contract, event, table, or component creates a credible blast radius. Stop gathering when the evidence is enough to choose scope, implementation order, validation, and recovery.

Read [references/reconstruction-and-decisions.md](references/reconstruction-and-decisions.md) when reconstructing a long history, conflicting documents, or an interrupted effort.

## 3. Guard scope before comparing solutions

Classify proposed work into three groups:

- **Required now:** directly necessary to produce the requested outcome safely.
- **Useful adjacent work:** independent defect or low-risk improvement that may be scheduled separately.
- **Future work:** security hardening, platform design, cleanup, or scale work not required by the current outcome.

An extra prerequisite enters blocking scope only when evidence shows the requested outcome cannot work safely without it. Technical elegance, future possibility, or the presence of adjacent debt is not proof.

Compare credible candidates on total product and system cost: behavior completeness, UX, architecture ownership, migration, runtime failure, authorization, data and traffic, maintenance, rollback, and deployment fit. Reject an optimization that merely moves cost elsewhere—for example, reducing event bytes while multiplying database reads and consumer complexity.

When credible candidates remain, show the comparison and a recommendation to the user; the user decides product trade-offs.

## 4. Design one active implementation

Name the existing owner that should hold each responsibility. Prefer the current feature owner, repository, gateway, store, hub, or domain command. A new abstraction must state its single responsibility, allowed callers, forbidden callers, and which old responsibility disappears.

When replacing an implementation, produce a Replacement Map:

| Question | Required answer |
|---|---|
| Why replace it? | Concrete defect, cost, or constraint in the old path |
| Who owns the new path? | One active owner |
| Which callers change? | Complete cutover set |
| What happens to old data/contracts? | Conversion or dormant deployment compatibility |
| How does failure behave? | Explicit error, retry, repair, or operator rollback |
| When is old code removed? | Observable retirement condition and negative gate |
| What is deliberately not carried over? | Old machinery the new path does not need (versioning, journals, metadata, editors, buckets), named so the replacement does not inherit it by default |

Do not design runtime fallback from a new implementation to the defective implementation it replaces. If rollback is required, prefer an observable deployment rollback, repair, or forward fix. Temporary old schema or API objects may remain only when the new runtime cannot call them, their owner and removal condition are named, and a negative check protects the boundary.

## 5. Produce a plan that another agent can execute

For Standard, migration, or multi-owner work, the canonical plan must close the decisions an implementer would otherwise invent:

- current behavior and target behavior;
- owner, callers, consumers, data, events, and error/recovery paths;
- affected modules and external objects;
- compatibility, migration, deployment order, and rollback;
- work packages ordered by real dependency;
- Acceptance Map from each important outcome or risk to evidence;
- stop conditions and remaining unknowns;
- explicit exclusions.

Each work package needs a reviewable outcome, scope, dependency, implementation steps, focused checks, integration or browser evidence when relevant, and failure handling. Mark the plan **Ready** only when work can begin without inventing material product behavior, architecture, data rules, or validation strategy.

A reviewer must be able to answer from the document alone: the problem and outcome, the verified current behavior and root cause, what was decided, rejected, deferred or left unknown, what changes, how the system behaves when something fails, the implementation order, the evidence for each outcome, and how rollout stops or rolls back. The reading path for that document is in the delivery contract below.

Use Reader Ready after the technical decisions are correct when the document is long or complex. Improve the reading path without removing constraints, evidence boundaries, negative cases, or implementation precision.

Read [references/delivery-contract.md](references/delivery-contract.md) for work-package, evidence, and state-transition rules.

## 6. Implement without reopening settled scope

Before editing, refresh the worktree and reread files that may have changed. Reproduce the problem or preserve a credible failing-before signal when practical. Modify the named owner and necessary consumers; do not create parallel paths to avoid understanding the current one.

If implementation reveals a fact that changes scope, architecture, migration, or acceptance, update the same canonical plan and decision ledger. Do not silently change direction.

Use fast checks during coding where they prevent compounding errors. Batch expensive shared-environment and Chrome verification by coherent release candidate:

```text
implement all code packages in the agreed batch
→ run focused and programmatic checks
→ fix failures as one feedback cycle
→ deploy the coherent candidate once
→ run the combined Staging or browser matrix
→ fix and repeat only the affected matrix
```

Do not redeploy and repeat the same hosted journey after every small package unless isolation, destructive risk, or a shared migration requires it.

## 7. Match evidence to the claim

An Acceptance Map chooses the lowest evidence layer that can prove each risk:

- static or unit evidence for pure rules and boundaries;
- component evidence for UI state and wiring;
- integration or database evidence for contracts, persistence, authorization, and atomicity;
- browser evidence for real user journeys, reload, focus, clipboard, download, responsive, and visual behavior;
- Hosted Staging for deployed Auth, RLS, RPC, Edge, Storage, Realtime, and environment wiring;
- Production canary for the final artifact and minimum critical path.

Record target, version, actor, data conditions, action, expected and actual result, and cleanup. Never borrow a passing result from a different SHA, environment, role, or older protocol. `UNVERIFIED` and `BLOCKED` are valid states; a test that was not run is not a pass.

Review the final diff against the original request, settled decisions, invariants, owner boundaries, and neighboring behavior. Search specifically for stale imports, dual writers/readers, hidden fallback, direct cross-layer calls, orphaned tests, and stale documentation.

## 8. Carry delivery to the authorized boundary

Keep the delivery states distinct and report each one separately: implemented locally (S4), programmatically verified (S5), deployed and verified on the target or Staging environment (S6), merged or promoted (S7), and deployed and verified on Production (S8).

Do not call work complete because code exists, CI is green, or a PR merged. Follow already-authorized CI, deployment, browser verification, readback, cleanup, screenshots, and documentation without waiting for the user to remind you. Stop only at a real failed gate, missing authorization, unsafe external state, or required product decision.

## 9. Leave a maintainable record

Update the smallest authoritative set:

- **Current State:** how the system works now;
- **Decision/ADR:** why a durable trade-off was chosen;
- **Plan or Todo:** only remaining work and explicit deferrals;
- **Evidence:** what was verified, where, and against which version;
- **Release/announcement:** user-relevant delivered change.

Do not leave completed construction plans looking active. Mark historical analysis as superseded or archive it. Avoid a second summary that repeats canonical facts. A future agent should understand the system from the maintained entrypoints, then inspect source only where freshness or detail requires it.

## Hard gates

Do not declare Ready or Complete when any applicable condition holds:

- the problem being solved is still ambiguous;
- a current behavior claim rests only on an old document;
- a live caller, consumer, actor, or writer is unclassified;
- the plan introduces a second active owner or hidden runtime fallback;
- a local optimization may increase total traffic, latency, privilege, or maintenance cost without measurement;
- learner, guest, background, reconnect, or recovery behavior could regress but is absent from the Acceptance Map;
- migration order, rollback, or external cleanup is undefined;
- evidence belongs to a different version or environment;
- documentation reports a later state than the code or deployment actually reached.

Read [references/field-lessons.md](references/field-lessons.md) before repairing a plan that has repeatedly expanded, contradicted itself, or passed tests while failing after deployment.
