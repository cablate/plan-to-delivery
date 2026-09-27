# Delivery state machine

Use this reference when work spans multiple decisions, work packages, environments, or sessions.

## One ledger, two levels

The **Delivery Ledger** is the resumable control surface. It contains:

- one top-level stage for the whole change;
- one row per independently progressing delivery unit;
- links to decisions and evidence rather than copied reports;
- the next executable action and why it is next.

Keep it in the canonical plan when one exists. If the task is too small for a formal plan, keep the same fields in the task's existing status or change artifact. Do not create a second ledger merely for reporting.

## Top-level record

```yaml
change: human-readable change name
mode: reconstruct | plan | execute | resume | repair
current_stage: S0..S9
freshness:
  repository: semantic repository name
  source_version: exact commit or artifact
  environment: local | staging | production | other
  checked_at: timestamp or date at useful precision
decision_status: open | settled | invalidated
next_action: one dependency-ready action
blockers: []
active_units: []
last_transition:
  from: Sx
  to: Sy
  evidence: links or identifiers
  judgment: human/source/JEV result when applicable
```

Use repository-specific formats only in a Project Profile. The YAML is illustrative; Markdown tables or structured issue fields are acceptable when they preserve the same semantics.

## Stages and gates

| Stage | Purpose | Exit evidence | Return here when |
|---|---|---|---|
| S0 Align intent | Fix outcome, actors, invariants, non-goals, and authorization | User intent is unambiguous enough to investigate | Product outcome or scope was misunderstood |
| S1 Reconstruct current state | Establish fresh source, runtime, owner, data, and history facts | Material claims are verified, bounded assumptions, or named unknowns | A current-state assumption is disproved |
| S2 Resolve decisions | Compare real candidates and settle trade-offs | Chosen direction, rejected options, reopen conditions | New evidence changes architecture, cost, UX, or risk |
| S3 Plan Ready | Produce the executable PlanSeal plan | Ready verdict and complete work/evidence graph | Implementer would need to invent a material decision |
| S4 Implement batch | Change every agreed source unit in the coherent batch | Units implemented; no unauthorized scope drift | Source or design needs correction |
| S5 Programmatically verify | Run focused, integration, database, type, boundary, and build checks | Applicable automated and readback gates pass | A programmatic check fails |
| S6 Verify deployed candidate | Deploy the fixed candidate and run combined hosted/browser evidence | Required journeys and cleanup pass on the stated candidate | Hosted wiring, behavior, or data fails |
| S7 Release Ready | Confirm release artifact, ordering, operations, and rollback | Release gates pass and final artifact is identified | Candidate changes or a release gate fails |
| S8 Verify Production | Run the authorized minimum production canary/readbacks | Critical production claims pass and residue is handled | Production behavior or parity fails |
| S9 Reconcile and close | Update current state, decisions, Todo, evidence, and release communication | No active plan masquerades as pending; remaining work is explicit | Closeout discovers drift or missing evidence |

`S6` and `S8` are conditional when the task has no deployed behavior or production scope. Mark them `not applicable` with a reason; do not call an unsupported environment verified.

## Delivery-unit record

Each unit represents an independently reviewable result, not merely a set of files.

| Field | Meaning |
|---|---|
| ID and name | Stable semantic label |
| Parent stage | Stage whose exit it helps prove |
| Owner | Existing product, architecture, data, service, or document owner |
| Status | proposed, ready, active, implemented, verified, failed, blocked, deferred, superseded, complete |
| Dependencies | Units or decisions that must already hold |
| Inputs | Facts, contracts, data, or artifacts consumed |
| Output | Observable result this unit produces |
| Invariants | Behavior it may not change |
| Evidence | Versioned checks or observations already obtained |
| Blocker | Specific missing condition; empty if none |
| Next action | One executable step, not a broad intention |
| Recovery point | Stage/unit to return to if it fails |
| Freshness | Last source/environment/version check |

## Unit transition rules

1. A unit becomes `ready` only when dependencies and required inputs exist.
2. `active` means work is actually in progress, not merely planned.
3. `implemented` says source or configuration exists; it does not imply verification.
4. `verified` names the evidence layer and version.
5. `complete` means the unit's output is accepted at the delivery boundary required by the change.
6. `blocked` names an external or decision condition that prevents meaningful progress. Use `failed` for a check that produced a corrective path.
7. `deferred` requires a reason and reopen condition.
8. `superseded` points to the replacing unit or decision.
9. Parent stage exit is derived from required units and gate evidence; it is not manually declared green while children disagree.

## Selecting the next action

On every resume:

1. Recheck whether the ledger's freshness anchor still matches the workspace and target environment.
2. Reconcile any unit changed by another agent, branch, deployment, or user decision.
3. Prefer a ready unit on the critical dependency path.
4. When several units are independent, batch or delegate only when ownership and acceptance are separable.
5. Avoid reopening complete units unless evidence became stale or a downstream failure points back to them.
6. Store the selected next action before starting long work, then update the result and transition afterward.

## JEV transition gates

JEV evaluates judgment, not progress bookkeeping.

| Transition or event | JEV use |
|---|---|
| S1 → S2 | Optional evidence-sufficiency check for a disputed or high-impact diagnosis |
| S2 → S3 | Required when multiple credible candidates or material architecture/cost/risk trade-offs remain |
| Before S3 exit | Hard-gate review for missing actors, dependencies, migration, fallback, rollback, and evidence |
| S4 or later reveals design-changing evidence | Re-evaluate only the affected decision; decide whether to return to S1, S2, or S3 |
| S6 → S7 | Optional release-risk review for unusual or high-impact residual risk; routine release gates remain deterministic |

Every saved JEV judgment records:

```text
transition/question
→ evidence version
→ real candidates
→ selected JEV profile
→ evaluator verdicts
→ hard-gate result
→ human/source reconciliation
→ transition allowed, denied, or conditional
```

One Router call selects the evaluation profile. Run one Evaluator per real candidate and escalate only for unresolved disagreement or a hard-gate need. Do not repeatedly reevaluate an unchanged decision.

## Compact tasks

For a small, reversible, single-owner change, stages may collapse:

```text
S0/S1 align and reproduce
→ S3/S4 short contract and implementation
→ S5 focused verification
→ S9 closeout
```

Collapsing notation does not permit skipping authorization, applicable hosted evidence, or an important recovery risk.
