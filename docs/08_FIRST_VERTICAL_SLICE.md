# 08. First Vertical Slice

## Purpose

Define the exact architectural slice that should be implemented first.

This document is intentionally strict.
It exists to prevent the project from spreading sideways too early.

The first true milestone is:

**boot -> shell -> broker -> native `ls`**

This proves the architecture is real.

---

## Goal of the Slice

Prove all of the following at once:

- the kernel can boot and host userspace
- a minimal shell exists
- the broker exists as a structured control plane
- object/file operations happen through the broker
- native tools are thin broker frontends
- broker actions are inspectable/logged

This slice is not about completeness.
It is about architectural proof.

---

## Included Components

### 1. Minimal kernel substrate

Included:
- boot path
- serial output/logging
- basic memory initialization
- interrupt/timer substrate as needed
- minimal scheduler/process/task support needed to host userspace

Not included:
- advanced driver framework
- full job/resource manager
- complex device isolation machinery
- broad hardware support

### 2. Minimal userspace foundation

Included:
- init
- one shell
- process launch path sufficient for shell + broker + `ls`

Not included:
- full process management UX
- rich shell language
- scripting runtime

### 3. Broker v0

Included:
- request ingress
- minimal request parsing
- action dispatch
- capability context shape
- logging path
- actions:
  - `ping`
  - `list_children`
  - `read_object`
  - `write_object`

Included as stubs:
- `job.plan`
- `resource.query`

Not included:
- subscriptions
- full policy engine
- distributed routing
- app action registration
- complete audit store

### 4. Native `ls`

Included:
- a simple `ls` command implemented only as broker frontend logic

Not included:
- independent filesystem traversal logic outside broker
- POSIX emulation path

### 5. Simple object backend

Included:
- simple object tree or filesystem-backed tree
- enough metadata to support `list_children`, `read_object`, `write_object`

Not included:
- object versioning
- snapshots
- rollback
- rich object kinds

---

## Explicit Layer Boundaries

### Kernel

Kernel is responsible for:
- execution substrate
- isolation primitives
- scheduler primitives
- memory and interrupt basics
- low-level substrate needed to run userspace services

Kernel is NOT responsible for:
- broker semantics
- job semantics
- resource policy
- most drivers as permanent in-kernel logic

### Userspace / Services

Broker and object backend live here.

This is where:
- semantic actions are interpreted
- object operations are routed
- logging happens
- future job/resource/device services will live

### Native tools

`ls` lives above broker as a thin frontend.

It does not define object semantics.
It consumes them.

---

## What Is Deferred On Purpose

Do not implement yet:
- real job scheduler
- resource manager
- driver-service restart framework
- Lua
- Python
- AI orchestrator
- networking
- versioned storage
- POSIX layer
- editor
- advanced shell

All of these matter.
None belong in the first vertical slice.

---

## How Job/Resource Architecture Is Preserved

Even though the first slice is file/object-centric, it must preserve the broader BrOS design.

Required preservation steps:
- reserve broker namespace for `job.plan`
- reserve broker namespace for `resource.query`
- keep vocabulary file in tree
- ensure broker logging records action name generically, not only file operations
- avoid naming that implies broker == filesystem service

This prevents the first slice from hardening into the wrong architecture.

---

## Acceptance Criteria

The first vertical slice is complete when a fresh boot can do this:

1. Kernel boots in QEMU.
2. System enters init.
3. Init starts shell.
4. Shell can send `ping` to broker.
5. Broker responds successfully.
6. Shell runs native `ls`.
7. `ls` calls `list_children` through broker.
8. Broker logs the action and result.
9. `read_object` and `write_object` work in simple form.
10. Stub calls to `job.plan` and `resource.query` return structured `not_supported` or synthetic responses.

If all ten are true, the architecture has crossed from concept into working form.

---

## Suggested Internal Development Order

1. Boot + serial logging
2. Basic userspace entry
3. Init
4. Shell
5. Broker skeleton
6. `ping`
7. Simple object backend
8. `list_children`
9. Native `ls`
10. `read_object`
11. `write_object`
12. Stub `job.plan`
13. Stub `resource.query`
14. Minimal broker log viewer/debug dump if easy

---

## Why This Slice Is Correct

This slice is the right one because it proves:
- minimal kernel discipline
- broker centrality
- native-first action model
- inspectable system behavior
- correct direction of future expansion

It is small enough to finish.
It is rich enough to validate the architecture.

---

## Key Rule

Do not optimize the first slice for features.

Optimize it for **architectural honesty**.
