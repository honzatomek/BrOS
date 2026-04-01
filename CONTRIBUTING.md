# Contributing to BrOS

Thank you for your interest in BrOS.

BrOS is a public hobby and learning project, but it is not intended to be an unstructured playground. It has a defined architectural center, a canonical documentation set, and a deliberate implementation direction. Contributions are welcome, but they should preserve that shape rather than blur it.

## 1. What BrOS is trying to build

BrOS is built around these architectural commitments:

- broker-centric control plane
- minimal kernel philosophy
- objects, actions, jobs, resources, and events as the core model
- native-first design
- AI, Lua, CLI tools, and later Python using one action model
- POSIX as compatibility, not architectural truth

If a contribution works against those commitments, it is not aligned with the project.

## 2. Before contributing

Please read the canonical documents first.

Suggested reading order:

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

For nontrivial changes, assume the canonical docs are binding unless they are being explicitly updated by the same contribution.

## 3. Current contribution priorities

At this stage, the most valuable work is work that proves the architecture in disciplined vertical slices.

The current practical focus is around the first true milestone:

**boot -> shell -> broker -> native `ls`**

That means contributions are especially welcome in areas such as:

- bootable kernel substrate
- userspace entry
- `init`
- minimal shell
- broker v0
- simple object backend
- broker-backed `ls`
- request logging and inspectability
- architecture-preserving stubs for `job.plan` and `resource.query`
- documentation corrections that improve consistency and reduce ambiguity

## 4. What a good contribution looks like

A good BrOS contribution usually does one or more of the following:

- strengthens the broker-centric design
- preserves or clarifies a documented invariant
- advances a real vertical slice
- reduces ambiguity in architecture or vocabulary
- improves inspectability, logging, or attributable behavior
- implements something small but structurally honest

Examples:

- a minimal broker request envelope with clean result and error shapes
- a thin broker-backed native tool
- a kernel change necessary for the first userspace slice
- a logging path that makes broker actions inspectable
- a documentation patch that resolves a real contradiction between documents

## 5. What to avoid

Some kinds of contributions are actively unhelpful at this stage.

Please avoid proposing or implementing:

- direct kernel bypass for meaningful control operations
- ad hoc side APIs that duplicate broker semantics
- POSIX-first redesigns that move BrOS back toward being a Unix clone
- hidden scheduler or resource truth that cannot be queried through the system model
- private semantics inside native tools that should live in broker/services
- feature sprawl that skips architectural proof
- speculative infrastructure that freezes the wrong abstractions too early

In short:

- do not bypass the broker for meaningful actions
- do not put policy into the kernel for convenience
- do not let compatibility layers become the architectural center
- do not collapse the broader model back into only files and processes

## 6. Architectural guardrails

Every serious contribution should be checked against these guardrails.

### Broker mediates meaningful actions

If a change introduces meaningful system behavior, ask whether it should be represented through the broker.

### Kernel stays minimal

The kernel is the execution substrate, not the place for high-level policy, AI semantics, or architectural drift.

### Jobs remain first-class

Heavy work and resource-sensitive work should preserve the job model.

### Resources stay explicit and queryable

Allocatable system capacity should not disappear into opaque implementation details.

### Events remain first-class

Meaningful state transitions should be observable.

### Native-first remains true

POSIX support matters later, but should not redefine the system’s core truth.

## 7. Development style

BrOS should be developed by **small, coherent, inspectable slices**.

Prefer:

- narrow patches
- explicit naming
- stable vocabulary
- structured result and error shapes
- implementation that follows the docs
- changes that preserve clean architectural boundaries

Avoid:

- hidden behavior
- giant speculative rewrites
- unrelated formatting churn
- premature framework-building
- temporary shortcuts that quietly redefine the system

## 8. Documentation expectations

Documentation is part of the architecture.

If your change affects any of the following, update the relevant canonical docs in the same contribution:

- terminology
- action names
- lifecycle states
- model boundaries
- invariants
- vertical-slice scope
- roadmap meaningfully

Likely affected documents include:

- foundation
- broker/control plane
- core models
- triggers/flows/invariants/scenarios
- state and event vocabulary
- developer guide
- roadmap

Implementation and docs should not drift apart.

## 9. Vocabulary discipline

BrOS relies on stable terms.

When contributing:

- use existing canonical vocabulary where it exists
- prefer extending vocabulary files over inventing synonyms
- keep job/resource/event terminology consistent
- do not casually replace job language with process language
- do not reduce objects to “just files” when the model is broader

If new vocabulary is truly needed, justify it and place it in the correct canonical document.

## 10. Proposing larger changes

For larger architectural changes, start with a written proposal before writing code.

A good proposal should explain:

- the problem being solved
- why the current architecture or documents are insufficient
- the exact change being proposed
- what documents and subsystems are affected
- why the change still aligns with the broker-centric model
- what complexity it introduces or removes

Large rewrites without framing are hard to evaluate and usually harder to merge.

## 11. Pull requests and patches

Good pull requests are easy to evaluate against the architecture.

Please include:

- a clear statement of intent
- affected subsystem(s)
- whether the change is code, docs, or both
- what milestone or slice it advances
- what remains intentionally out of scope

Prefer focused patches. When practical, separate mechanical cleanup from semantic change.

## 12. Testing posture

Tests should reinforce the system model, not encourage shortcuts around it.

Prefer tests that validate:

- broker request and result behavior
- explicit denial or success paths
- logging and inspectability
- first-slice flow correctness
- object action correctness
- continuity of reserved job/resource namespace
- event emission where applicable

## 13. Licensing model

BrOS is currently intended to start with a **simple MPL-2.0 + DCO model**.

### Code license

Project code is intended to be licensed under **Mozilla Public License 2.0 (MPL-2.0)**.

The reason for this choice is practical:

- the project remains public and forkable
- contributions are encouraged
- improvements to covered files remain open when distributed
- the project does not start with heavier legal machinery than it needs

### Contribution model

BrOS is intended to use a **Developer Certificate of Origin (DCO)** rather than a CLA at the start.

This means contributors sign off their commits to certify that they have the right to submit the work under the project license.

See `DCO.md` for the full text.

In practice, sign-off typically means adding this line to a commit:

```text
Signed-off-by: Your Name <you@example.com>
```

A typical workflow is:

```bash
git commit -s
```

The `-s` adds the sign-off line automatically.

### AI-assisted contributions

AI-assisted contributions are allowed.

However, the DCO sign-off must always be made by a human contributor, not by an AI assistant or automated tool.

By signing off, the human contributor confirms that they:

- reviewed the entire contribution
- take responsibility for the submitted patch
- have the right to submit all included material under the project license
- did not knowingly include material with unclear or incompatible provenance

Contributions with unclear provenance may be rejected.

## 14. Governance and code of conduct

The file `ai_assistant_code_of_conduct.md` is a standalone governance document.

It may be referenced where relevant, but it should remain separate from the main architecture docs.

## 15. Maintainer review and project direction

BrOS is open to contributions, but project direction remains curated.

Being public and forkable does not mean every contribution will be merged. The maintainer may decline changes that:

- conflict with the broker-centric architecture
- introduce the wrong abstractions too early
- create drift against the canonical docs
- trade architectural honesty for convenience

That is not meant to discourage contribution. It is meant to keep the project coherent.

## 16. What success looks like right now

At this stage, the best contribution is usually not the biggest one.

The best contribution is one that makes BrOS more real without making it less itself.
