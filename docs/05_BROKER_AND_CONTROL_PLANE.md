# 05. Broker and Control Plane

## Table of Contents

1. Purpose
2. Role of the Broker
3. Why the Broker Exists
4. Responsibilities
5. Risks and Early Mitigation
6. Broker v0 Minimal Contract
7. Broker Action Model
8. Result Model
9. Capability Model for v0
10. Logging and Audit Model for v0
11. Object Semantics for v0
12. Stub Action Requirements
13. Non-Goals for Broker v0
14. First Slice Acceptance Criteria
15. Broker API v0.1 Extended Action Surface
16. Key Rule

## 1. Purpose

This document defines the broker as the semantic control plane of BrOS and captures both the foundational rationale and the minimal implementation contract required for the first architectural milestone.

## 2. Role of the Broker

The broker is the semantic core of the system.

It mediates all meaningful actions.

## 3. Why the Broker Exists

Traditional systems fragment control across:

- syscalls
- shells
- app APIs

The broker unifies this into one coherent control model.

Its key insight is simple:

Same verbs, different authority.

AI, Lua, Python, CLI, and applications can share one action vocabulary while differing in capabilities and policy.

## 4. Responsibilities

The broker is responsible for:

- validating action packets
- enforcing capabilities
- routing to services
- logging actions
- applying policy

The broker is the place where semantics, permissions, logging, and routing meet.

## 5. Risks and Early Mitigation

### 5.1 Risks

- becoming a bottleneck
- API instability early

### 5.2 Early mitigation

- keep action schema minimal and extensible
- avoid premature complexity

## 6. Broker v0 Minimal Contract

### 6.1 Purpose

Broker v0 defines the smallest broker contract that is precise enough to start implementation without freezing the full long-term schema.

It is intentionally narrow.

It exists to support the first architectural milestone:

**boot -> shell -> broker -> native `ls`**

It must remain aligned with the BrOS core:

- broker-centric control plane
- native-first model
- minimal kernel
- structured actions
- inspectable behavior

### 6.2 Scope of Broker v0

Broker v0 is not the full semantic control plane.

Broker v0 exists to prove:

- structured action ingress
- capability-aware action handling
- service routing shape
- result envelope shape
- audit/logging path
- native frontend integration

Broker v0 should implement only the minimal action set needed to validate the architecture.

### 6.3 Action Set for v0

#### Core actions

- `ping`
- `list_children`
- `read_object`
- `write_object`

#### Reserved-but-stubbed actions

These should exist conceptually in the namespace, but may return "not implemented" in early slices:

- `job.plan`
- `resource.query`

The reason to reserve them now is architectural continuity.

BrOS should not accidentally collapse into a file-only broker model during the first implementation phase.

## 7. Broker Action Model

Each broker action has four conceptual parts:

1. action name
2. target
3. arguments
4. context

### 7.1 Action name

A stable verb or namespaced verb, such as:

- `ping`
- `list_children`
- `read_object`
- `write_object`
- `job.plan`
- `resource.query`

### 7.2 Target

The object or logical endpoint the action applies to.

Examples:

- root object
- directory-like object
- file-like object
- broker itself
- future job or resource object

### 7.3 Arguments

Action-specific parameters.

Examples:

- path or object handle
- offset or length
- create or overwrite mode
- structured query parameters later

### 7.4 Context

Execution metadata supplied by the caller or surrounding runtime.

Minimum v0 conceptual fields:

- caller identity
- capability context
- session or process origin
- request id
- timestamp or monotonic sequence later

## 8. Result Model

Every action should return a structured result envelope with:

- `ok` or `error`
- typed result payload if successful
- structured error reason if not
- metadata
- audit identity

### 8.1 Success result

A success result should conceptually contain:

- action echoed or correlated
- target echoed or resolved
- returned value or payload
- optional metadata

### 8.2 Error result

Errors should not be opaque strings only.

They should have at least a stable reason class.

Suggested v0 error classes:

- `invalid_action`
- `invalid_target`
- `invalid_arguments`
- `denied`
- `not_found`
- `not_supported`
- `conflict`
- `backend_failure`

## 9. Capability Model for v0

Capability handling in v0 should be minimal but present.

Broker v0 does not need a full security architecture, but it must preserve the shape of one.

Minimum conceptual rule:

- every request carries a capability context
- broker checks capability context before dispatch
- denied actions fail explicitly
- logs include capability-sensitive origin information

Possible early implementation simplification:

- a single privileged shell context
- one unprivileged context later
- hard-coded policy table

That is acceptable, as long as the broker surface preserves the model.

## 10. Logging and Audit Model for v0

Every broker action should be loggable.

Minimum log record shape:

- request id
- caller or origin
- action
- target
- outcome
- timestamp

Do not overbuild full durable audit storage yet.

A ring buffer, append-only text log, or simple structured log is sufficient for v0.

The important thing is that broker actions are inspectable.

## 11. Object Semantics for v0

Broker v0 may use a simple path-based object model at first, but must treat it conceptually as object access rather than raw syscall passthrough.

### 11.1 `list_children`

Returns child entries of a directory-like object.

### 11.2 `read_object`

Returns bytes or text content of a readable object.

### 11.3 `write_object`

Writes content to an object, creating or replacing according to arguments and policy.

### 11.4 `ping`

Broker health or connectivity sanity check.

## 12. Stub Action Requirements

### 12.1 `resource.query`

For the first slice, this may return a fixed or synthetic response.

But the action name and result shape category should exist.

Purpose:

- preserve the resource-object architecture
- avoid later namespace breakage
- allow shell or UI debugging hook

### 12.2 `job.plan`

For the first slice, this may return:

- `not_supported`
- or a synthetic planning result

Purpose:

- preserve job semantics in the broker namespace
- prevent accidental regression into process-only thinking

## 13. Non-Goals for Broker v0

Do not implement yet:

- full job scheduler
- full resource manager
- event subscriptions
- object versioning semantics
- app action registration
- network or distributed routing
- POSIX translation path

Those belong to later slices.

## 14. First Slice Acceptance Criteria

Broker v0 is sufficient when all of the following are true:

1. Shell can send a broker request.
2. Broker can parse and dispatch the request.
3. `ping` succeeds.
4. `list_children` works on a simple object tree.
5. Native `ls` is implemented as a thin broker frontend.
6. Every broker call is logged.
7. `job.plan` and `resource.query` exist in the namespace, even if stubbed.

## 15. Broker API v0.1 Extended Action Surface

### 15.1 Job actions

- `job.submit`
- `job.validate`
- `job.plan`
- `job.start`
- `job.pause`
- `job.resume`
- `job.cancel`

### 15.2 Resource actions

- `resource.list`
- `resource.query`
- `resource.watch`
- `resource.allocations`
- `resource.reserve`
- `resource.release`

### 15.3 Constraint actions

- `constraint.evaluate`
- `constraint.attach`
- `constraint.detach`

### 15.4 Rule

All actions go through the broker.

## 16. Key Rule

Broker v0 must be small but architecturally honest.

It should not overbuild the future.

It should not violate the future either.
