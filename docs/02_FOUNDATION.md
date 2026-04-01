# 02. FOUNDATION

## Table of Contents

1. Purpose
2. The Core Shift
3. Core Model
4. System Layers
5. Responsibilities
6. Scheduling Principle
7. Orchestrators
8. Driver Isolation
9. Native vs POSIX
10. Logging and History
11. Design Principles
12. Native Trigger, Schedule, and Time Model
13. Reactive System Model
14. Final Insight

## 1. Purpose

This document defines the core conceptual model of BrOS.

It is not an API specification.
It is not an implementation guide.

It is the ground truth of how the system is meant to be understood.

All other documents must align with this.

## 2. The Core Shift

Traditional systems are built around:

- processes
- files
- syscalls

This system is built around:

> objects, actions, jobs, and events

This is the fundamental paradigm shift.

## 3. Core Model

### 3.1 Objects

Objects represent state.

Examples:

- files
- directories
- datasets
- models
- devices
- resources
- applications
- messages

Objects may be:

- persistent
- ephemeral
- virtual

Objects are inspectable.

### 3.2 Actions

Actions represent intent.

Everything meaningful happens through actions:

- `read_object`
- `write_object`
- `list_children`
- `submit_job`
- `query_resource`
- `send_message`

All actions:

- go through the broker
- are validated
- are capability-scoped
- are logged

There are no hidden control paths.

### 3.3 Jobs

Jobs represent structured work.

A job is not a process.

A job defines:

- intent
- resource needs
- constraints
- scheduling class
- lifecycle

A job may be:

- interactive
- background
- batch
- maintenance

Processes are implementation details of jobs.

Heavy compute is always expressed as a job.

### 3.4 Resources

Resources represent allocatable and measurable system capacity.

Examples:

- CPU
- memory
- GPU
- storage bandwidth
- network bandwidth
- power
- thermal headroom

#### 3.4.1 Key Principle

All allocatable resources must be broker-visible and queryable.

Resources are exposed as objects:

- `resource://cpu`
- `resource://memory`
- `resource://gpu/0`
- `resource://power/system`
- `resource://thermal/cpu`

Each resource exposes:

- inventory
- capacity
- allocation
- usage
- pressure
- policy

There is no hidden scheduler-only state.

### 3.5 Constraints

Constraints represent conditions over execution and scheduling.

Types:

- hard
- soft
- dynamic
- relational

Examples:

- requires GPU
- max memory
- only on AC power
- avoid thermal throttling
- not co-located with another job

Constraints are explicit and inspectable.

### 3.6 Events

Events represent observable state transitions.

Examples:

- `job_submitted`
- `job_started`
- `job_completed`
- `resource_pressure_changed`
- `thermal_mode_changed`
- `power_state_changed`

Events are:

- emitted by the system
- delivered through the broker
- subscribable

## 4. System Layers

```text
User / UI / CLI
↓
Orchestrators (AI, Lua, Python, apps)
↓
Broker (actions, policy, logging)
↓
System services (jobs, resources, storage, network)
↓
Kernel (execution primitives)
↓
Hardware
```

## 5. Responsibilities

### 5.1 Kernel

- execution
- scheduling primitives
- memory management
- interrupts

The kernel does not implement:

- policy
- job semantics
- resource allocation decisions

### 5.2 Broker

The broker is the control plane.

Responsibilities:

- validate actions
- enforce capabilities
- route actions
- expose resource state
- coordinate job submission
- log all meaningful activity

### 5.3 Services

Services implement:

- job system
- resource tracking
- storage
- networking

They provide data and execution mechanisms.

## 6. Scheduling Principle

Scheduling is broker-driven, based on:

- job intent
- resource state
- constraints
- policy

### 6.1 Critical Rule

No resource allocation without broker-queryable resource state.

## 7. Orchestrators

Multiple orchestrators exist:

- AI
- Lua
- Python later
- CLI tools
- native apps

They use the same action model and differ only in capabilities.

> Same verbs, different authority.

## 8. Driver Isolation

Drivers are not part of the core kernel by default.

- Drivers run as isolated device services.
- Failures degrade resources, not the system.
- Kernel remains a minimal execution substrate.

Driver failure leads to resource degradation, not reboot.

## 9. Native vs POSIX

### 9.1 Native model

- objects
- actions
- jobs
- resources
- events

### 9.2 POSIX layer

- processes
- file descriptors
- syscalls

POSIX is a translation layer, not the architectural center.

## 10. Logging and History

All meaningful actions are:

- logged
- attributable
- replayable where possible

Storage supports:

- versioning
- snapshots
- rollback

## 11. Design Principles

- broker is the control plane
- kernel is the execution plane
- jobs are first-class
- resources are explicit and queryable
- constraints are structured, not implicit
- events are first-class
- AI is an orchestrator, not a privileged entity
- no hidden system behavior
- everything meaningful is inspectable

## 12. Native Trigger, Schedule, and Time Model

### 12.1 Purpose

BrOS includes a native trigger model so that time, recurrence, event reaction, and conditional execution are part of the architecture rather than delegated to external, loosely integrated tools.

This replaces the need to treat scheduling as an afterthought handled by cron-like systems, polling scripts, or ad hoc background services.

### 12.2 Why This Model Exists

Traditional scheduling mechanisms such as cron are useful, but they have structural limitations:

- they are external to the system's semantic model
- they are generally string-based rather than structured
- they are unaware of jobs, resources, and constraints
- they are weakly integrated with capabilities and auditability
- they do not naturally express event-driven or state-driven reactions
- they encourage parallel control planes instead of one unified one

BrOS therefore treats scheduling and triggering as native broker-governed concepts.

### 12.3 Core Concept

A trigger is a condition that causes an action.

This unifies:

- timers
- schedules
- event reactions
- state-based conditions
- hybrid conditional automation

The trigger model makes it possible to express time-sensitive and condition-sensitive system behavior through the same action model used elsewhere in the system.

### 12.4 Trigger Structure

A trigger consists of:

- condition
- action
- context
- optional constraints
- enable/disable state
- record/history linkage

#### 12.4.1 Condition

The condition describes when the trigger should fire.

#### 12.4.2 Action

The action describes what broker action should be invoked when the condition is satisfied.

#### 12.4.3 Context

The context carries origin and capability information so the trigger does not become an unaccountable bypass.

#### 12.4.4 Optional constraints

Constraints may further restrict when or how the trigger fires, especially under changing resource conditions.

#### 12.4.5 Enable/disable state

Triggers should be manageable as first-class objects that can be enabled or disabled without being deleted.

#### 12.4.6 Record/history linkage

Trigger activity should be attributable and observable just like other meaningful system behavior.

### 12.5 Trigger Types

#### 12.5.1 Time-based triggers

Examples:

- at a specific timestamp
- after a duration
- every fixed interval
- on a recurring schedule

#### 12.5.2 Event-based triggers

Examples:

- on job completion
- on resource pressure change
- on device failure
- on power-state change

#### 12.5.3 State-based triggers

Examples:

- memory pressure exceeds threshold
- system becomes idle
- GPU becomes available
- thermal state returns to normal

#### 12.5.4 Composite triggers

Examples:

- every hour and system idle
- on AC power and GPU available
- after job completion if memory pressure is below threshold

### 12.6 Execution Model

Triggers do not exist to execute arbitrary hidden code as a parallel control plane.

Their role is to invoke broker actions.

In many cases that means:

- submitting a job
- resuming a job
- pausing a job
- issuing an object action
- changing system state through explicit action invocation

This preserves architectural honesty.

### 12.7 Trigger Interaction with Jobs

Triggers and jobs are closely related but not identical.

- A trigger expresses when something should happen.
- A job expresses what structured work should be performed.

Typical interactions include:

- trigger submits a job
- trigger pauses or resumes a job
- trigger creates maintenance work
- trigger re-attempts deferred work when conditions improve

This allows BrOS to represent time and reactivity without conflating them with work itself.

### 12.8 Trigger Interaction with Resources

Because triggers are native to the system model, they can take resource state seriously.

That means a trigger can be shaped by conditions such as:

- only fire when GPU is available
- only fire on AC power
- only fire when system is idle
- defer until memory pressure falls

This is significantly stronger than a blind wall-clock scheduler.

### 12.9 Trigger Interaction with Events

Triggers can react to events directly.

This enables a reactive system model:

`event → trigger → action`

Examples:

- `resource.pressure_changed` → trigger pauses background indexing
- `job.completed` → trigger submits post-processing job
- `power.changed` → trigger suspends non-essential maintenance work
- `driver.failed` → trigger marks a workflow degraded or reroutes future submissions

### 12.10 Trigger Interaction with Capabilities

Triggers must not become unbounded automation loopholes.

Every trigger therefore belongs under a capability and origin model. A trigger should carry enough context that the system can answer:

- who created it
- under what authority it exists
- what actions it is allowed to invoke
- how its activity should appear in logs and records

### 12.11 Trigger Management Surface

The architecture should reserve a native action surface for trigger control.

Representative actions include:

- `trigger.create`
- `trigger.delete`
- `trigger.list`
- `trigger.enable`
- `trigger.disable`
- `trigger.inspect`
- `trigger.history`

The exact transport schema may be deferred, but the conceptual surface should be present early enough that the system does not harden around external cron-style assumptions.

### 12.12 Timers vs Schedules vs Triggers

#### Timer

A timer is a simple time-based condition.

#### Schedule

A schedule is a recurring or patterned time condition.

#### Trigger

A trigger is the general native concept that can include timers, schedules, events, state predicates, and compositions of them.

This hierarchy keeps the architecture simple:

- timers and schedules are special cases
- trigger is the native umbrella concept

### 12.13 Why This Matters

This native trigger model replaces:

- cron jobs
- polling scripts
- loosely coupled timers
- ad hoc daemons that exist only to watch and react

with:

- structured conditions
- explicit actions
- broker-mediated behavior
- inspectable records
- capability-aware automation

### 12.14 Key Principle

Time is not external to the system model.

Time is one of the dimensions through which system state and system behavior evolve.

## 13. Reactive System Model

### 13.1 Purpose

BrOS should be understood not only as an action-driven system, but also as a reactive one.

### 13.2 Why

Once the architecture includes:

- events as first-class change
- triggers as native conditions
- jobs as structured work
- resources as visible state

the system stops being merely command-driven in the traditional sense.

It becomes able to react to its own conditions in a native, inspectable way.

### 13.3 Model

The basic reactive chain is:

`state / event → trigger / policy → action → job or state change → event → record`

This means the operating system can express:

- reactive maintenance
- event-driven workflow chaining
- resource-sensitive automation
- power- and thermal-aware adaptation
- runtime recovery behavior
- deferred work under suitable conditions

without forcing that behavior into external glue layers.

### 13.4 Value

This is one of the strongest reasons BrOS is better matched to modern computing practice than systems whose automation model remains mostly shell scripts, cron jobs, and private service logic.

## 14. Final Insight

The system is not centered around processes or files.

It is centered around:

> intent (actions), state (objects), work (jobs), capacity (resources), and change (events)

That is the foundation.
