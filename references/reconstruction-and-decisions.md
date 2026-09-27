# Reconstructing intent and decisions

Use this reference when a request has accumulated many conversations, plans, branches, or partial implementations.

## Build a bounded timeline

Collect only events that changed at least one of these:

- requested outcome or non-goal;
- product behavior or actor contract;
- chosen architecture or owner;
- data, authorization, migration, or deployment assumptions;
- verification status;
- decision to proceed, defer, preserve, replace, or stop.

Do not reproduce the conversation. Convert it into a decision ledger:

| Decision | Trigger/evidence | Current status | Supersedes | Reopen condition |
|---|---|---|---|---|

The latest user decision wins over an older proposal. Current code and runtime decide what exists; they do not decide what the user intended. A historical plan explains rationale but cannot prove implementation or deployment.

## Use an evidence hierarchy

For each material claim, keep the strongest applicable source:

1. runtime readback for deployed behavior;
2. exact-version source, schema, configuration, and tests;
3. current canonical current-state or ADR documents;
4. merged diff and release evidence;
5. historical plans, reports, screenshots, and conversation summaries.

Lower-ranked sources can explain why or generate a hypothesis. They cannot overwrite contradictory higher-ranked evidence without a new check.

## Explain repeated revisions

When a document changed repeatedly, do not conclude that the authors were merely indecisive. Identify the new evidence that forced each revision. Common causes include:

- the original problem was narrower than the proposed platform solution;
- an actor or caller was missing from the first inventory;
- a local optimization moved cost to another subsystem;
- a test proved code behavior but not hosted deployment parity;
- a compatibility fallback prevented the old path from retiring;
- a plan described intended behavior as if it were current behavior;
- the user clarified a product trade-off after seeing concrete impact.

Write only inferences that are supported by the sequence. Mark them as inference when no explicit decision record exists.

## Stop condition

Reconstruction is complete when the agent can state:

- the current requested outcome;
- the decisions still in force;
- the actual current implementation and deployment state;
- which older proposals are superseded;
- the remaining decisions or evidence that can still change the plan.

More history after this point is archival, not a planning prerequisite.
