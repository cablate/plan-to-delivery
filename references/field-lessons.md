# Field lessons that shaped this workflow

These cases came from a real interactive-product codebase used to calibrate this skill. They are not universal architecture rules. Each case captures a recurring planning failure and the constraint that prevents it.

## 1. A heartbeat problem nearly became an authorization platform

**What happened:** A costly classroom heartbeat was broadcast to every learner. Early planning expanded toward private channels, custom tokens, Realtime policies, signing keys, presenter leases, and a new protocol. The immediate goal only required learners to send heartbeat messages without subscribing to the heartbeat topic, while the existing classroom hub remained the instructor-side subscriber.

**Why plans kept changing:** The first plans optimized the imagined final architecture before proving which parts were necessary to stop the fan-out.

**Constraint learned:** Start from the narrow observable loss. Every blocking prerequisite must prove that the requested outcome cannot work safely without it. Put security hardening and platform redesign in future work unless they are genuine prerequisites.

## 2. Making an event smaller could have made the whole system busier

**What happened:** A shared realtime submission event carried a rich payload. Replacing it with a small hint looked cheaper, but many consumers would then independently query the database. Event traffic could fall while database requests, latency, and ownership complexity rose.

**Why the proposal stopped:** The team had no comparative evidence for total realtime bytes, readback request count, and user-visible latency.

**Constraint learned:** Optimize the whole path, not one meter. Require before/after cost across transport, readback, latency, authorization, and maintenance. Keeping the current design can be the correct decision.

## 3. A safe-looking fallback kept the defect alive

**What happened:** A new summary reader reduced heavy list reads, but malformed data could silently send runtime back to the legacy full-row reader. This preserved apparent availability while making it impossible to know which design was active and prevented the legacy path from retiring.

**Why the rule became stricter:** The old path existed because it was costly and hard to maintain. Using it as the new path's fallback reintroduced the same risk precisely when the new path failed.

**Constraint learned:** One active runtime path. Fail clearly, repair data, or roll back the deployed artifact. Dormant compatibility objects are acceptable only when the new runtime cannot call them and their removal is tracked.

## 4. Staff authorization could have locked learners out of class

**What happened:** A role and membership project initially treated project membership as the obvious read boundary. Learners were guests with classroom credentials rather than staff accounts or memberships. Rewriting shared project and session policies could therefore block joining, slide loading, reconnect recovery, submissions, and assets.

**Why the plan was re-sliced:** The missing actor was not an edge case; it was the main classroom user path.

**Constraint learned:** Inventory actors and principals before editing authorization. Preserve independent guest contracts while adding staff paths. Permission plans require positive and negative role matrices plus the real recovery journey.

## 5. Green code did not mean the deployed database matched it

**What happened:** A release passed CI and Staging evidence, but Production initially lacked a required restore RPC. Learners could submit, yet refreshing did not restore completed state. A Production canary exposed the mismatch; the fix added the migration and a release contract gate.

**Why earlier evidence was insufficient:** Tests proved the code and a different environment, not the final web/database pairing.

**Constraint learned:** Bind evidence to exact version and environment. Cross-layer releases need deployment-order readbacks and a minimum Production canary. Never infer hosted parity from Git history alone.

## 6. Testing every small package separately slowed the release without adding confidence

**What happened:** Each small work package was implemented, deployed, and walked through in Chrome before the next package began. Most journeys repeated the same setup and neighboring behavior, creating long idle periods and fragmented evidence.

**Why the process changed:** The useful isolation already came from focused and programmatic checks. Hosted verification was most efficient against a coherent candidate with one combined journey matrix.

**Constraint learned:** Keep fast feedback during coding, then batch expensive shared-environment tests. Repeat only the affected matrix after a failure. Separate batching from skipping evidence.

## 7. Too many reports made current state harder to know

**What happened:** Research notes, implementation plans, verification reports, current-state documents, and historical analyses described the same capability at different dates. Later agents read an old plan as pending work or treated a Staging result as Production state.

**Why documents were repeatedly consolidated:** More text increased search results but decreased confidence about authority and freshness.

**Constraint learned:** Maintain one owner per fact type: current behavior, durable decision, remaining work, evidence, and release impact. Mark completed plans historical or superseded. A canonical entrypoint should navigate; it should not duplicate every detail.

## 8. Code-managed announcements turned a content edit into a full software release

**What happened:** Updating a product announcement changed repository content and triggered broad CI and deployment work. The content workflow was later moved behind a governed Agent/API capability with drafts, revisions, validation, and explicit publishing.

**Why this matters beyond announcements:** Operational friction often signals that ownership is placed in the wrong delivery system.

**Constraint learned:** Match the change path to the product responsibility. Keep review and authorization, but do not force low-risk content through unrelated build and classroom gates.

## Applying the stories

Use a story only when its causal pattern matches the current task. Do not cargo-cult the exact solution. The reusable questions are:

- Are we solving the observed problem or an imagined platform future?
- Did we map every principal, caller, consumer, and recovery path?
- Is there exactly one active owner and runtime path?
- Does the proposed optimization reduce total cost?
- Does the evidence belong to the version and environment being claimed?
- Are repeated tests buying new information?
- Can a future maintainer find the current truth without reading the whole history?

## Calibration provenance

The stories were reconstructed on 2026-09-27 from the calibration repository's current source, Git history, and these maintained artifacts:

- `docs/architecture/realtime/ADR-001-heartbeat-minimal-split.md`
- `docs/verification/realtime-heartbeat-minimal-split-2026-09-11/README.md`
- `docs/CONSENSUS-TODO.md`
- `docs/DESIGN-PATTERN-PRINCIPLES.md`
- `docs/planning/SYNORA-SINGLE-SITE-AUTHORIZATION-IMPLEMENTATION.md`
- `docs/AI-ENGINEERING-DELIVERY-PRINCIPLES.md`
- `docs/RELEASE-ACCEPTANCE-PRINCIPLES.md`
- `docs/audit/CURRENT-CYCLE-RELEASE-IMPACT-2026-09-27.md`
- `docs/planning/SYNORA-AGENT-PRODUCT-UPDATES-PLAN.md`

These paths preserve traceability for maintainers of this skill. They are calibration evidence, not runtime dependencies and not facts about another project.
