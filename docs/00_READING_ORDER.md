# 00. BrOS Canonical Documentation Set

## 0.1 Purpose

This directory is the canonical standalone documentation set for BrOS.

It is structured as a numbered reading sequence so the architecture can be understood in layers:

1. mission and system necessity
2. foundational model
3. long-form design rationale
4. execution and control architecture
5. broker and API surface
6. core semantic models
7. triggers, flows, invariants, and scenarios
8. first implementation slice
9. development posture
10. native applications
11. storage and history
12. POSIX compatibility
13. roadmap and outlook
14. frozen shared vocabulary

A separate governance file remains standalone:

- `ai_assistant_code_of_conduct.md`

It is part of the project documentation set, but it is not merged into the architectural documents.

## 0.2 Reading Order

### Core conceptual path

1. `01_VISION_AND_MISSION.md`
2. `02_FOUNDATION.md`
3. `03_DETAILED_REFERENCE.md`
4. `04_KERNEL_AND_SYSTEM_ARCHITECTURE.md`
5. `05_BROKER_AND_CONTROL_PLANE.md`
6. `06_CORE_MODELS.md`
7. `07_TRIGGERS_FLOWS_INVARIANTS_AND_SCENARIOS.md`

### Implementation path

8. `08_FIRST_VERTICAL_SLICE.md`
9. `09_DEVELOPER_GUIDE.md`
10. `10_NATIVE_APPLICATIONS.md`
11. `11_STORAGE_VERSIONING_AND_HISTORY.md`
12. `12_POSIX_COMPATIBILITY.md`
13. `13_ROADMAP_AND_OUTLOOK.md`
14. `14_STATE_AND_EVENT_VOCABULARY.md`

## 0.3 Canonical Rules for Reading the Set

- BrOS is broker-centric.
- All meaningful actions go through the broker.
- The kernel is the execution substrate, not the semantic center.
- Jobs are first-class.
- Resources are explicit and queryable.
- Events represent meaningful change.
- AI is an orchestrator, not a privileged bypass.
- Native architecture is the core truth.
- POSIX is compatibility, not identity.

## 0.4 File Map

- `01_VISION_AND_MISSION.md` — mission, necessity, positioning, and architectural motivation
- `02_FOUNDATION.md` — core conceptual model and foundational rules
- `03_DETAILED_REFERENCE.md` — long-form rationale and design synthesis
- `04_KERNEL_AND_SYSTEM_ARCHITECTURE.md` — layered architecture, kernel role, orchestrators, services, and AI alignment
- `05_BROKER_AND_CONTROL_PLANE.md` — broker role, broker v0 contract, and broker API surface
- `06_CORE_MODELS.md` — jobs, resources, constraints, events, scheduling, and runtime behavior
- `07_TRIGGERS_FLOWS_INVARIANTS_AND_SCENARIOS.md` — reactive model, trigger model, invariants, canonical flows, and scenarios
- `08_FIRST_VERTICAL_SLICE.md` — the first strict implementation slice
- `09_DEVELOPER_GUIDE.md` — implementation posture and contribution rules
- `10_NATIVE_APPLICATIONS.md` — native application roadmap
- `11_STORAGE_VERSIONING_AND_HISTORY.md` — storage, history, rollback, and audit linkage
- `12_POSIX_COMPATIBILITY.md` — compatibility philosophy and limits
- `13_ROADMAP_AND_OUTLOOK.md` — staged roadmap and VM/VPN/tensor outlook
- `14_STATE_AND_EVENT_VOCABULARY.md` — frozen minimum shared vocabulary
- `ai_assistant_code_of_conduct.md` — standalone governance document
