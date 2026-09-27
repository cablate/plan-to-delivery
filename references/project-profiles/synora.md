---
profile: synora-interactive-engine
status: optional personal profile
reviewed_against: interactive-engine main, 2026-09-27
---

# Synora interactive-engine profile

Load this profile only after confirming the workspace is the Synora `interactive-engine` repository. It adapts the generic workflow; current source, hosted readbacks, applicable `AGENTS.md`, and canonical project documents remain authoritative.

## Start here

- `docs/planning/CURRENT-STATUS.md` — what can currently be claimed.
- `docs/CONSENSUS-TODO.md` — remaining work, explicit deferrals, and reopen conditions.
- `docs/AI-ENGINEERING-DELIVERY-PRINCIPLES.md` — engineering execution responsibility.
- `docs/TEST-ARCHITECTURE-PRINCIPLES.md` — evidence-layer selection.
- `docs/RELEASE-ACCEPTANCE-PRINCIPLES.md` — post-merge Staging and Production duties.
- `docs/architecture/ARCHITECTURE.md` and relevant feature/current-state document — owner and runtime behavior.
- `docs/audit/supabase-traffic-2026-09-23/README.md` — canonical Supabase domain navigation when data, RLS, RPC, Edge, Storage, or Realtime is involved.

Historical plans and dated audits are leads. Recheck them against current source and hosted state before using them as present facts.

## Product and architecture invariants

- Student learners are Guest Principals with classroom credentials, not Staff memberships. Staff authorization work must preserve join, slide load, submission, score feedback, reload, reconnect, and asset access.
- Use existing feature owners, repositories, gateways, stores, and the Classroom Hub. Do not create a second Realtime owner, general-purpose Supabase service, or page-local permission/data layer.
- One active runtime implementation is required. Do not add hidden fallback to a replaced reader, writer, RPC, parser, transport, or protocol.
- Durable database state owns recovery; Realtime is notification/control where documented. Do not treat an event payload as durable truth without an explicit contract.
- Assess Supabase cost across Realtime messages, database requests, Edge invocations, payload bytes, latency, and maintenance. Lowering one metric is not enough.
- Agent Runtime exposes user-level Synora capabilities. Browser device controls and infrastructure operations are not ordinary Agent commands.

## Delivery and evidence fit

- `dev` feeds Hosted Staging; `main` feeds Production. Bind evidence to the exact candidate or equivalent immutable artifact/tree.
- During coding, run focused and relevant programmatic checks. Finish the agreed code batch before the combined Hosted Staging Chrome matrix unless a migration or destructive risk requires isolation.
- Staging may be used for authorized integration evidence. Keep browser, database/RPC/RLS, Edge, Storage, and cleanup claims separate.
- After `dev` merge, run the basic classroom journey plus every user-visible flow affected by the batch. Capture screenshots for changed visuals or release documentation.
- After `main` deployment, use the designated Production canary for the minimum classroom flow: open/login, presenter broadcast, learner join, slide sync, submit, instructor receipt/score when applicable, learner reload recovery, stop/restart, and precise cleanup. Production scope still follows current authorization and release rules.
- Do not require mobile unless the change affects viewport, touch, background/foreground, network switching, or an explicit mobile contract.

## Recurring stop conditions

- A hosted classroom is active during a high-risk DB/Web deployment.
- Required migration, RPC, Edge, Storage, or environment parity is missing.
- A learner flow regresses while tightening Staff authorization.
- The candidate differs from the version that passed Staging evidence.
- A proposed optimization lacks total traffic and UX evidence.
- Test cleanup would remove data outside the named fixture scope.

Use TypeSafe AI JEV for genuine Synora architecture, data, authorization, migration, UX, or cost trade-offs. Preserve the judgment with the affected state transition; do not run it merely to restate an already evidenced single path.
