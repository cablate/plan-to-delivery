# Plan to Delivery

`plan-to-delivery` is a Codex skill for carrying a product or engineering change from an unclear request to a verified delivery.

It gives the agent a repeatable way to reconstruct the current system, settle real decisions, produce an implementation-ready plan, implement the authorized work, collect the right evidence, and leave the project documentation in a trustworthy state.

> 中文快速說明：這個 Skill 把「需求對齊 → 現況查證 → 方案決策 → 施工規劃 → 開發 → 測試 → 上線 → 文件收尾」串成一條可恢復的流程。使用者不需要反覆提醒 AI 要查 codebase、確認影響範圍、補測試、做實機驗收或更新文件。

## When to use it

Use this skill for work that crosses several steps or owners, including:

- a new product capability;
- a cluster of related bugs;
- a refactor or architecture change;
- a database, API, protocol, or deployment migration;
- an interrupted task that must resume from branches, pull requests, environments, and existing evidence;
- a plan that must reach a formal **Ready for Development** decision.

Skip it for a trivial isolated edit or a purely exploratory question that does not need a delivery plan.

## What it changes about the workflow

Without a shared workflow, an agent may treat old documents as current truth, expand a local problem into a platform rewrite, leave a hidden legacy fallback, test every small change separately, or declare completion without matching the deployed version.

Plan to Delivery prevents those failures by requiring one evidence chain:

```text
intent
→ current facts
→ explicit decisions
→ executable plan
→ implementation
→ programmatic evidence
→ deployed evidence
→ current-state documentation
```

Long-running work uses a resumable state machine:

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

Each independent work unit records its status, dependencies, evidence, blocker, next action, recovery point, and freshness anchor. A later agent can resume from that ledger instead of guessing from conversation history.

## Optional companions: PlanSeal and JEV

Plan to Delivery works on its own. It is the **orchestrator**: it owns the full chain and decides when a specialist would help.

| Capability | Responsibility |
|---|---|
| **Plan to Delivery** | Reconstructs facts, controls scope, manages delivery state, coordinates implementation and verification, and closes documentation. |
| **PlanSeal / `method-plan`** | Produces the formal implementation plan, Acceptance Maps, work packages, migration reasoning, and Ready verdict. |
| **TypeSafe AI JEV** | Gives a structured second opinion when credible alternatives, architecture trade-offs, cost/risk judgments, or hard gates need evaluation. |

Both are optional. When installed, PlanSeal can produce the formal plan for Standard, migration, or multi-owner work, and JEV can review decision gates where judgment adds value; neither runs mechanically for an obvious low-risk change. Without them, the skill applies the same checks from source evidence.

The agent may not claim a PlanSeal or JEV result that did not run.

## Install

Clone this repository into the Codex skills directory.

Windows default location:

```powershell
git clone https://github.com/cablate/plan-to-delivery.git "$env:USERPROFILE\.codex\skills\plan-to-delivery"
```

macOS or Linux default location:

```bash
git clone https://github.com/cablate/plan-to-delivery.git ~/.codex/skills/plan-to-delivery
```

Restart or refresh Codex after installation. PlanSeal and the TypeSafe AI JEV skill are optional; install them if you want their formal plan or second-opinion outputs.

## Use

Invoke it explicitly after the intended outcome and constraints are understood:

```text
Use $plan-to-delivery to investigate this issue, produce an implementation-ready plan,
implement the authorized scope, verify it, and reconcile the project documents.
```

You may also constrain the delivery boundary:

```text
Use $plan-to-delivery through Plan Ready only. Do not modify product code.
```

```text
Use $plan-to-delivery to implement and verify locally and on staging.
Do not deploy to production.
```

The skill does not create authorization. Deployments, destructive actions, external communications, and other consequential mutations still follow the user's instructions and the environment's governing rules.

## Outputs

Depending on the task size and authorized boundary, the workflow produces or updates:

- a verified current-state and problem statement;
- a decision ledger with rejected options and reopen conditions;
- one canonical implementation plan;
- a Delivery Ledger with stage and work-unit status;
- an impact map covering owners, callers, data, permissions, UX, migration, and recovery;
- Acceptance Maps and focused, integration, browser, device, staging, or production evidence;
- rollback and stop conditions;
- current-state, Todo, evidence, and release documentation after delivery.

It does not create every document for every task. Small changes collapse adjacent stages and retain only the evidence needed for their actual risk.

## Project Profiles

The core skill is project-neutral. A Profile adds stable project or user context without copying the whole codebase into the skill.

Profiles layer in this order:

```text
generic skill rules
→ optional user-preference overlay
→ optional project profile
→ current task decisions and authorization
```

A Profile may point to canonical documents, name environments and owners, define recurring verification journeys, record critical invariants, and describe stable delivery preferences. It may not weaken hard gates, grant permissions, include secrets, or replace fresh source and runtime evidence.

### Add a Profile

1. Copy [`references/profile-template.md`](references/profile-template.md).
2. Give it a semantic name and choose `kind: user-preference` or `kind: project`.
3. Define when it applies. For a project profile, use stable repository markers such as the remote, root files, package name, or a canonical project document.
4. Link to maintained sources instead of copying volatile architecture or runbook content.
5. Add only rules that change planning or delivery decisions.
6. Record what version or date the profile was reviewed against.
7. Name the profile from the repository's `AGENTS.md`, select it explicitly, or place it in one of the supported locations below.

Suggested locations:

```text
User preference:
$CODEX_HOME/skills/plan-to-delivery/references/user-profiles/<name>.md

Bundled fallback profile:
$CODEX_HOME/skills/plan-to-delivery/references/project-profiles/<name>.md

Project-owned profile:
A path named by that repository's AGENTS.md or canonical AI-engineering documentation
```

Selection order is explicit user choice, then a project-local profile named by repository instructions, then a matching bundled profile, then the generic workflow. If a profile conflicts with current source or a higher instruction, the conflicting claim is treated as stale until verified.

The bundled [Synora profile](references/project-profiles/synora.md) is a concrete example. It is loaded only after the workspace markers match Synora; it does not affect other projects.

Read the full [Profile contract](references/project-profile-contract.md) before publishing a reusable profile.

## Repository layout

```text
SKILL.md                              Main orchestration rules
agents/openai.yaml                    Codex display metadata
references/state-machine.md           Stage gates and resumable unit state
references/delivery-contract.md       Delivery and verification contract
references/reconstruction-and-decisions.md
                                      Current-state and decision method
references/field-lessons.md           Failure patterns and their safeguards
references/project-profile-contract.md
                                      Profile selection and boundaries
references/profile-template.md        Starting point for a new profile
references/project-profiles/synora.md Optional project-specific example
```

## Design principles

- Current source and runtime evidence outrank historical plans.
- Extra prerequisites must prove they are necessary to the requested outcome.
- New and legacy implementations do not silently run in parallel.
- A fallback must be an explicit recovery design, not a hidden return to known-bad behavior.
- Programmatic checks run before combined hosted verification.
- Evidence belongs to the exact candidate version being delivered.
- The canonical plan and ledger own status; reports link to them instead of copying stale state.
- Completed work ends with current-state and Todo reconciliation.

## Contributing

Keep the generic skill project-neutral. Put organization or repository specifics in a Profile. When adding a rule, document the failure mode it prevents and avoid turning one historical incident into a universal requirement.

Before committing changes, validate the skill structure with the Codex skill validator available in your environment.
