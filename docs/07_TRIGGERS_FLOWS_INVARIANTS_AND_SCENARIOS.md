# 07. Triggers, Flows, Invariants, and Scenarios

## Table of Contents

1. Purpose
2. Cross-Layer Invariants
3. Canonical Flow
4. Canonical Scenarios
5. Architectural Insight

## 1. Purpose

This document makes the BrOS architecture operational.

It spells out the cross-layer invariants that must remain true, the canonical end-to-end flow of meaningful work, and concrete scenarios that show how the system behaves under realistic conditions.

## 2. Cross-Layer Invariants

### 2.1 Invariant 1 — Broker mediates all meaningful actions

All meaningful system actions pass through the broker.

#### Meaning

If an operation matters at the semantic level of the system, it belongs in the broker action model.

#### Implications

- there are no hidden control paths
- services do not become private semantic islands
- native tools do not become small competing control planes
- automation, scripting, AI, and applications all share one conceptual action surface

#### Failure mode if violated

The system fragments into unrelated APIs, private hooks, and special-case tools. Logging, capabilities, and policy stop being coherent.

### 2.2 Invariant 2 — Jobs are first-class

Substantial work is represented as jobs, not merely as raw process execution.

#### Meaning

The semantic unit of work is the job. Processes remain important as execution mechanisms, but they do not define the work model.

#### Implications

- scheduling can reason about intent
- runtime control can reason about degradation, pause, resume, and checkpoint behavior
- user-visible and orchestrator-visible work maps to a durable semantic unit

#### Failure mode if violated

The architecture silently collapses back into process-only thinking, and compute-aware scheduling becomes an accidental bolt-on instead of a core system behavior.

### 2.3 Invariant 3 — Resources are explicit and queryable

All allocatable resources are broker-visible and queryable.

#### Meaning

Resources are not just hidden internal scheduler structures. They are part of the system’s semantic truth.

#### Implications

- planning and admission can use shared resource truth
- runtime adaptation can react to explicit resource state
- orchestrators can reason about capacity without guessing
- policy becomes inspectable

#### Failure mode if violated

Resource allocation becomes opaque again, making jobs, planning, and runtime control conceptually weak and hard to audit.

### 2.4 Invariant 4 — Constraints are typed

Constraints are typed conditions, not loose comments or private scheduler hints.

#### Meaning

Constraints belong to stable categories:

- hard
- soft
- dynamic
- relational

#### Implications

- planning can reason consistently
- runtime adaptation can preserve semantics
- the architecture has a common language for execution conditions

#### Failure mode if violated

Constraint semantics drift across services, making planning and runtime behavior inconsistent and difficult to trust.

### 2.5 Invariant 5 — Admission is not runtime

Admitting work and controlling work during execution are distinct phases.

#### Meaning

A job can be admitted correctly and still require runtime adaptation later due to changing conditions.

#### Implications

- events matter after admission
- degradation is not treated as architectural surprise
- pause, resume, throttle, and checkpoint behavior has a natural place
- planning does not pretend the world stays fixed

#### Failure mode if violated

The system overfits to initial placement and becomes brittle under pressure, failures, or changing resource conditions.

### 2.6 Invariant 6 — Events are universal

Meaningful state transitions emit events.

#### Meaning

Events are not optional diagnostics. They are part of how the system expresses change.

#### Implications

- system behavior remains observable
- reactive automation becomes possible
- triggers can operate natively
- runtime coordination remains grounded in explicit change

#### Failure mode if violated

The system becomes semantically opaque, and reactive control devolves into polling, guesswork, or hidden coupling.

### 2.7 Invariant 7 — Capability-scoped authority applies everywhere

All meaningful actions occur under authority context.

#### Meaning

The same verbs can be available across orchestrators, but authority differs.

#### Implications

- AI, Lua, Python, CLI, and apps can share the same action model without sharing the same power
- logging can attribute behavior correctly
- automation remains governed instead of ambiently privileged

#### Failure mode if violated

The architecture either fragments into special-case trust models or drifts into unsafe ambient authority.

### 2.8 Invariant 8 — Kernel remains minimal

The kernel is the execution substrate, not the semantic center of the system.

#### Meaning

Policy, high-level coordination, and most semantic meaning remain above the kernel.

#### Implications

- faster evolution above kernel
- smaller trusted core
- less architectural fossilization
- clearer separation between execution and control

#### Failure mode if violated

Meaning drifts into privileged low-level code, making the system harder to evolve and less honest about its layered design.

### 2.9 Invariant 9 — Durable record exists

Meaningful system behavior should leave durable, attributable traces.

#### Meaning

Inspectability is not satisfied by transient behavior alone.

#### Implications

- broker logging matters
- job record matters
- storage and version history can be linked to actions
- the system can explain what happened and why

#### Failure mode if violated

The architecture loses one of its core promises: semantic accountability.

### 2.10 Invariant 10 — Native-first model remains the core truth

Native abstractions define the future of the system.

#### Meaning

Compatibility layers exist to recover practical value, not to define the architecture.

#### Implications

- native tools and services matter early
- POSIX remains translation, not identity
- roadmap decisions are judged against native coherence first

#### Failure mode if violated

The system gradually becomes a conventional compatibility environment with broker-shaped decoration.

### 2.11 Invariant 11 — Driver failures are contained

Driver and device failures are represented as resource or service-level events and state where possible, not as automatic kernel-collapse semantics.

#### Meaning

Failure should become modeled runtime condition rather than immediate architectural catastrophe whenever feasible.

#### Implications

- degradation is expected
- jobs can react
- resources can become withdrawn or unavailable
- restart and recovery semantics remain possible

#### Failure mode if violated

The architecture loses one of its strongest distinctions from monolithic hidden-failure designs.

## 3. Canonical Flow

The canonical flow is:

`observe → formulate → submit → validate → plan → admit → bind → run → events → record`

This is the architectural path through which meaningful system work becomes explicit, scheduled, observable, and attributable.

## 4. Canonical Scenarios

### 4.1 Scenario 1 — Native object listing through broker

A user boots the system into the minimal shell and runs native `ls`.

Flow:

1. The shell launches the native `ls` tool.
2. `ls` does not traverse the object space directly as an authority in its own right.
3. Instead, it issues a `list_children` action to the broker.
4. The broker validates the request, checks the caller context, and routes the action to the object backend.
5. The object backend returns child entries for the target object.
6. The broker wraps the result in the standard result envelope.
7. The action and outcome are logged.
8. `ls` displays the result.

Why this scenario matters:

- proves broker centrality
- proves native tool thinness
- proves object access path
- proves inspectability of meaningful operations

### 4.2 Scenario 2 — AI submits inference work with GPU preference

An AI orchestrator needs to perform inference and prefers GPU acceleration.

Flow:

1. AI formulates a job submission action under its capability context.
2. The broker receives the action and validates origin and permissions.
3. Job planning inspects relevant resources, especially GPU availability and current pressure.
4. If a compatible GPU resource is available, the system admits the job accordingly.
5. Runtime begins.
6. During runtime, GPU pressure rises or the device degrades.
7. The system emits resource and possibly driver or device events.
8. Runtime control re-evaluates the job.
9. Depending on policy and job constraints, the job may:
   - continue
   - degrade
   - throttle
   - pause
   - reschedule later
10. All meaningful state transitions remain observable.

Why this scenario matters:

- shows AI as an ordinary orchestrator, not an exception
- shows jobs, resources, and events working together
- shows that runtime is not assumed static
- shows that failure becomes state rather than mystery

### 4.3 Scenario 3 — Background indexing pauses under memory pressure

A background indexing job is running while the machine remains interactively in use.

Flow:

1. The indexing workload is represented as a background job.
2. It is admitted under current resource conditions.
3. Later, memory pressure rises due to interactive user activity.
4. `resource.pressure_changed` is emitted.
5. Runtime control evaluates which job classes should yield.
6. Background work is throttled or paused.
7. Interactive tasks retain responsiveness.
8. The system record shows what happened and why.

Why this scenario matters:

- shows scheduling classes matter semantically
- shows resource pressure is a native part of the system model
- shows runtime adaptation without collapse
- shows the advantage over hidden heuristic-only scheduling

### 4.4 Scenario 4 — Thermal event throttles active work

The machine enters a thermally constrained state.

Flow:

1. Thermal conditions cross a defined threshold.
2. `thermal.changed` is emitted.
3. Related resources may move to degraded state.
4. Running jobs are re-evaluated according to:
   - scheduling class
   - constraints
   - current system policy
5. Some jobs throttle.
6. Some may pause.
7. Interactive work may remain prioritized.
8. The system remains running and understandable.

Why this scenario matters:

- shows thermal conditions as first-class state
- shows resources and events are not limited to CPU and GPU inventory counts
- shows architecture can express graceful degradation

### 4.5 Scenario 5 — Driver failure does not collapse the machine

A GPU driver service fails.

Flow:

1. The driver service exits or faults.
2. `driver.failed` is emitted.
3. A related resource may become:
   - degraded
   - unavailable
   - withdrawn
4. Jobs using that resource are re-evaluated.
5. Depending on constraints and runtime control policy:
   - some jobs fail explicitly
   - some degrade
   - some pause
   - some wait for recovery
6. The rest of the system continues running.
7. Recovery may later emit rebound or restarted events.

Why this scenario matters:

- shows the difference between service failure and kernel failure
- shows resource withdrawal as valid runtime condition
- demonstrates contained failure semantics

### 4.6 Scenario 6 — Triggered maintenance runs only when system is idle and on AC power

A system maintenance task should run periodically, but only under safe conditions.

Flow:

1. A trigger is defined with:
   - periodic schedule
   - condition: system idle
   - condition: AC power
   - action: submit maintenance job
2. The trigger remains enabled in the system.
3. Time condition is reached.
4. Current state is checked.
5. If system is not idle or not on AC, the trigger does not fire yet.
6. When all conditions are satisfied, the broker invokes the configured action.
7. A maintenance job is submitted.
8. Trigger firing and job submission are recorded.

Why this scenario matters:

- shows why native triggers are stronger than cron
- shows interaction of time, state, and jobs
- shows capability-controlled automation without polling scripts

### 4.7 Scenario 7 — Job completion triggers dependent work

A workflow includes post-processing after a main job succeeds.

Flow:

1. Primary job runs to completion.
2. `job.completed` is emitted.
3. An event-based trigger listening for that event activates.
4. The trigger invokes a broker action to submit a dependent job.
5. The dependent job is planned and admitted according to current resource conditions.
6. The workflow remains observable through actions and events rather than hidden application glue.

Why this scenario matters:

- shows event-driven workflow chaining
- shows native reactive orchestration
- shows broker-mediated workflow composition without external orchestration hacks

### 4.8 Scenario 8 — Power change causes pause and resume behavior

The system moves from AC to battery power.

Flow:

1. `power.changed` is emitted.
2. Power-related resources and conditions are updated.
3. Jobs with relevant constraints are re-evaluated.
4. Non-essential or power-sensitive jobs may pause.
5. When AC power returns, later events may allow triggers or runtime control to resume or re-admit work.

Why this scenario matters:

- shows power as a first-class system concern
- shows dynamic constraints matter at runtime
- shows time, event, and constraint integration

### 4.9 Scenario 9 — VM-heavy or remote-backed use remains architecturally coherent

The system runs inside a VM or coordinates work against resources that are not tied to one local physical machine.

Flow:

1. Orchestrators continue to use the same action model.
2. Objects, jobs, and resources remain semantically stable.
3. Backing implementation changes, but architecture does not split into a second conceptual universe.
4. Logging, capabilities, and resource visibility remain valid concerns.
5. Future transport and federation can extend the model instead of replacing it.

Why this scenario matters:

- shows the architecture is not brittle or purely local
- preserves future VM, VPN, and distributed coherence
- proves the broker model is transport-friendly by design

## 5. Architectural Insight

Failure becomes state, not catastrophe.

Taken together, the invariants, flow, and scenarios show that BrOS is semantically unified, resource-aware, capability-governed, event-driven, reactive, and inspectable across layers.
