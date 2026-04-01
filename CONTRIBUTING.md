# Contributing to BrOS

Thank you for contributing to BrOS.

BrOS is not a conventional operating system project with a generic pile of subsystems waiting to be filled in. It is an architecture-driven project with a specific conceptual center:

- broker-centric control plane
- minimal kernel philosophy
- objects, actions, jobs, resources, and events as the core model
- native-first design
- AI, Lua, CLI, and later Python using one action model
- POSIX as compatibility, not architectural truth

Contributions are welcome, but they must preserve that architecture.

## 1. Before you contribute

Please read the canonical documentation before proposing structural changes.

Recommended reading order:

1. `README.md`
2. `docs/canonical/01_VISION_AND_MISSION.md`
3. `docs/canonical/02_FOUNDATION.md`
4. `docs/canonical/05_BROKER_AND_CONTROL_PLANE.md`
5. `docs/canonical/06_CORE_MODELS.md`
6. `docs/canonical/07_TRIGGERS_FLOWS_INVARIANTS_AND_SCENARIOS.md`
7. `docs/canonical/08_FIRST_VERTICAL_SLICE.md`
8. `docs/canonical/09_DEVELOPER_GUIDE.md`
9. `docs/canonical/13_ROADMAP_AND_OUTLOOK.md`
10. `docs/canonical/14_STATE_AND_EVENT_VOCABULARY.md`

If your proposed change conflicts with those documents, the change is not ready.

## 2. Contribution priorities

The project should progress in a disciplined order.

Current high-priority contribution areas are expected to be:

- bootable kernel substrate
- userspace entry and `init`
- minimal shell
- broker v0
- simple object backend
- broker-backed native `ls`
- logging and inspectability for the first slice
- architecture-preserving stubs for `job.plan` and `resource.query`

Contributions that help prove the first vertical slice are usually better than ambitious subsystem expansions.

## 3. What a good contribution looks like

A good BrOS contribution does at least one of these well:

- strengthens the broker-centric architecture
- preserves or clarifies a system invariant
- implements an end-to-end vertical slice
- reduces ambiguity in the canonical model
- improves inspectability, logging, or attributable behavior
- moves the project toward the first real milestone without hardening the wrong abstractions

Examples of good contribution shape:

- a minimal broker request envelope with clean logging
- a thin broker-backed native tool
- a structured object action with clear error classes
- a small kernel improvement required to host the first userspace slice
- a documentation correction that resolves a real inconsistency

## 4. What not to contribute

Some kinds of contributions are actively harmful at this stage.

Do not propose or implement:

- direct kernel bypasses for meaningful control operations
- ad hoc side APIs that duplicate broker semantics
- POSIX-first architecture that redefines the project around Unix compatibility
- process-only language where the model requires jobs
- hidden resource allocation truth that cannot be queried through the broker-visible model
- feature-heavy additions that skip the first-slice proof
- speculative infrastructure that freezes the wrong abstractions too early

In practical terms:

- do not make native tools implement private semantics that belong in broker/services
- do not turn the broker into a filesystem-only API by neglecting reserved job/resource namespace continuity
- do not place policy and semantic behavior into the kernel because it feels expedient
- do not let compatibility layers dictate the native model

## 5. Architectural guardrails

Every nontrivial contribution should be checked against these guardrails.

### 5.1 Broker mediates all meaningful actions

If a change introduces a meaningful system action, ask whether that action belongs behind the broker.

### 5.2 Kernel remains minimal

The kernel is the execution substrate, not the policy engine and not the semantic center.

### 5.3 Jobs are first-class

Heavy work and resource-sensitive execution should be expressed in a way that keeps the job model intact.

### 5.4 Resources must be explicit and queryable

Do not hide allocatable system capacity inside uninspectable scheduler internals.

### 5.5 Events are universal

Meaningful state transitions should produce explicit observable state, not only silent side effects.

### 5.6 Native-first remains true

POSIX support matters later, but it must not redefine the architectural center.

## 6. Preferred development style

BrOS should be developed by vertical proof, not by broad speculative sprawl.

Prefer:

- small, complete, inspectable slices
- minimal interfaces with clean future continuity
- explicit error/result shapes
- clear naming and stable vocabulary
- implementation that follows the documentation exactly
- mechanical consistency across docs, code, logs, and tool behavior

Avoid:

- hidden behavior
- premature generalization
- framework-heavy overengineering
- giant refactors with no architectural proof
- “temporary” shortcuts that redefine the architecture by accident

## 7. Documentation requirements

Documentation is not optional support material in BrOS. It is part of the architecture.

If you change:

- terminology
- action names
- model boundaries
- lifecycle states
- invariants
- first-slice scope
- roadmap meaningfully

then update the relevant canonical documents in the same change set.

At minimum, check whether your contribution affects:

- `FOUNDATION`
- broker/control plane docs
- core models
- triggers/flows/invariants/scenarios
- state and event vocabulary
- developer guide
- roadmap

Documentation and implementation must not drift apart.

## 8. Naming and vocabulary discipline

BrOS depends on stable vocabulary.

When contributing:

- use existing canonical terms where they already exist
- prefer extending vocabulary files rather than inventing synonyms
- keep job states, event names, and resource terms consistent
- do not casually substitute process language for job language
- do not collapse objects into “just files” when the model is broader

If new vocabulary is truly necessary, justify it and place it into the appropriate canonical document.

## 9. Proposing larger changes

For larger architectural changes, start with a written proposal before implementation.

A strong proposal should state:

- the problem being solved
- which current document or invariant is insufficient
- what exact change is proposed
- what files and subsystems are affected
- why the change does not violate the broker-centric architecture
- what future complexity is avoided or introduced

Large unframed rewrites are much harder to review than disciplined, explicit proposals.

## 10. Pull request guidance

A pull request should be easy to evaluate against the architecture.

Please include:

- a clear statement of intent
- the affected subsystem(s)
- whether the change is architectural, implementation, documentation, or mixed
- how the change aligns with the canonical docs
- what milestone or vertical slice it advances
- what remains intentionally out of scope

Useful pull requests are usually narrow enough that their architectural effect can be understood without guesswork.

## 11. Commit and patch expectations

Favorable contribution style:

- keep patches focused
- separate mechanical renames from semantic changes where practical
- avoid unrelated formatting churn
- preserve intent in diffs
- make code structure reflect architectural boundaries

When possible, one patch should do one coherent thing.

## 12. Testing posture

Tests should prove architectural behavior, not merely satisfy superficial mechanics.

Prefer tests that verify:

- broker request/response behavior
- capability-aware denial or success paths
- logging and inspectability
- first-slice action flow
- object action correctness
- stub continuity for future job/resource namespace
- event emission where introduced

As the project grows, tests should reinforce the system model rather than encourage shortcuts around it.

## 13. Licensing expectations

BrOS uses **MPL-2.0** as the project code license.

Contributors should preserve license headers and notices once the repository structure formalizes them.

If you add a new file, place it under the correct project area and ensure it inherits or declares the correct license according to repository policy.

## 14. AI-assisted contributions

AI-assisted contributions are allowed.

However:

- only a human contributor may sign off under the DCO
- the human signer must review the entire contribution
- the human signer must have the right to submit all included material under the project license
- unclear provenance may be rejected during review

Using an AI assistant does not shift responsibility away from the human contributor.

## 15. Code of conduct and governance

The file `ai_assistant_code_of_conduct.md` is a standalone governance document.

It may be referenced where relevant, but it should remain structurally separate and should not be merged into the architectural documentation.

## 16. The current standard of success

At this stage, the best contribution is usually not the most ambitious one.

The project’s current standard of success is still:

**boot -> shell -> broker -> native `ls`**

That milestone proves:

- kernel substrate
- userspace viability
- broker centrality
- object access through broker
- native tool shape
- logging foundation
- architectural honesty

Contribute in a way that helps the project cross that line cleanly.

## 17. Final rule

If a contribution makes BrOS easier to build but less faithful to its architecture, it is not a good contribution.

Protect the model first.
