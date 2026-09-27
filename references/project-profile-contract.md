# Project Profile contract

A Project Profile adapts the generic workflow to a repository, product, organization, or user's maintained preferences. It is optional and must remain smaller than the project's canonical documentation.

## Discovery order

Use the first applicable maintained source, while obeying higher-level instructions:

1. Explicit profile named by the user.
2. Project-local profile named by applicable repository instructions.
3. A bundled profile whose declared workspace markers match.
4. Generic workflow with no profile.

Do not select a profile solely because the product name appears in conversation. Confirm repository markers such as remote, root files, package name, or canonical project document. If two profiles match, use the more local authoritative one or ask only when their differences affect the work.

Profiles can be layered when their responsibilities differ:

```text
generic skill rules
→ explicit user-preference overlay
→ matching project profile
→ current task decisions and authorization
```

The overlay may choose presentation, batching, reporting, or preferred review habits. The project profile owns project facts, environments, invariants, and maintained workflow. A preference cannot override a safety gate, repository rule, project fact, or current explicit instruction.

## Profile fields

A useful profile may define:

```yaml
profile: semantic name
applies_when:
  repository_markers: []
canonical_entrypoints: []
owner_map: []
environments: []
delivery_flow: []
critical_invariants: []
verification_journeys: []
mutation_boundaries: []
preferred_tools: []
documentation_rules: []
user_preferences: []
freshness:
  reviewed_against: version or date
```

Use prose or tables when clearer. Do not add fields with no decision value.

## Allowed customization

- point to authoritative current-state, architecture, runbook, and Todo entrypoints;
- name product-specific actors, owners, services, and environments;
- define release branches, hosted targets, fixtures, and canary journeys;
- state project-specific invariants and known dangerous shortcuts;
- specify which evidence layer is required for recurring risks;
- record stable user preferences such as batching expensive verification;
- identify optional companion skills and tools available in that workspace.

## Forbidden customization

A profile cannot:

- override system, developer, user, repository, or skill permissions;
- grant deployment, production mutation, communication, or destructive authorization;
- weaken the generic hard gates or relabel missing evidence as pass;
- hardcode secrets, credentials, private identities, or expiring session data;
- duplicate large architecture documents, runbooks, or current-state inventories;
- turn a historical incident into a universal rule without a maintained project decision;
- force tools that are unavailable or unnecessary for the current task.

## Maintenance

Profiles are navigation and policy overlays, not a second source of truth. Link to the owner document instead of copying volatile facts. Include a freshness marker, and downgrade conflicting profile claims to a hypothesis until checked.

When a project needs a local profile, prefer a maintained file under its own agent or documentation governance. A bundled profile is useful as a personal default, a portable example, or a fallback for a workspace that has not yet adopted a local one.

Suggested locations, subject to the workspace's own governance:

- user overlay: `$CODEX_HOME/skills/plan-to-delivery/references/user-profiles/<name>.md`;
- bundled project fallback: `$CODEX_HOME/skills/plan-to-delivery/references/project-profiles/<name>.md`;
- project-owned profile: a path named by the repository's `AGENTS.md` or canonical AI-engineering documentation.

Use [profile-template.md](profile-template.md) as a starting point. Delete empty sections rather than maintaining placeholder policy.
