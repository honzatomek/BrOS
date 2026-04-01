# 14. State and Event Vocabulary v0

## Purpose

Freeze the minimum shared vocabulary needed so early code does not drift away from the BrOS conceptual model.

This is not the final exhaustive vocabulary.
It is the smallest stable semantic backbone needed for the first implementation slices.

---

## Design Principle

Freeze **meaning**, not full transport encoding.

The goal is that:
- kernel code
- broker code
- shell/tools
- later job/resource services

all speak the same conceptual language even before final schema decisions.

---

## Job Vocabulary

### Job states

The full conceptual model is richer, but v0 should freeze the following names now:

- `draft`
- `submitted`
- `validated`
- `queued`
- `blocked`
- `admitted`
- `binding`
- `starting`
- `running`
- `degraded`
- `throttled`
- `paused`
- `checkpointing`
- `resuming`
- `completed`
- `failed`
- `cancelled`
- `rejected`
- `expired`

### Minimum implementation set

The first implementation slice does not need to realize all states.

But these must be treated as reserved vocabulary now:
- `submitted`
- `queued`
- `running`
- `completed`
- `failed`
- `cancelled`
- `rejected`

And these should be reserved explicitly for later:
- `blocked`
- `admitted`
- `binding`
- `degraded`
- `throttled`
- `paused`
- `checkpointing`
- `resuming`
- `expired`

---

## Resource Vocabulary

### Resource availability states

Freeze these names now:

- `available`
- `degraded`
- `unavailable`
- `restarting`

### Resource views

A resource may expose:
- `inventory`
- `capacity`
- `allocation`
- `usage`
- `pressure`
- `policy`

The first slice may only implement a subset, but these are the stable conceptual categories.

---

## Device / Driver Vocabulary

This is important and must be preserved from the recent design update.

### Driver model

- drivers are isolated device services by default
- driver failure is not kernel failure
- device/resource loss is an observable system event
- resource withdrawal is a valid runtime condition

### Driver/device conditions

Reserve these semantic conditions:
- `healthy`
- `degraded`
- `failed`
- `restarting`
- `withdrawn`

These do not all need first-slice implementation, but they must be part of the architectural vocabulary now.

---

## Constraint Vocabulary

Constraint classes:
- `hard`
- `soft`
- `dynamic`
- `relational`

Constraint outcomes:
- `satisfied`
- `unsatisfied`
- `violated`
- `unknown`
- `overridden`

The broker and later services should use these categories consistently.

---

## Core Event Vocabulary

### Job events

Freeze these names:
- `job.submitted`
- `job.validated`
- `job.queued`
- `job.blocked`
- `job.admitted`
- `job.running`
- `job.degraded`
- `job.throttled`
- `job.paused`
- `job.resumed`
- `job.completed`
- `job.failed`
- `job.cancelled`
- `job.rejected`

### Resource events

Freeze these names:
- `resource.capacity_changed`
- `resource.pressure_changed`
- `resource.allocation_changed`
- `resource.degraded`
- `resource.unavailable`
- `resource.restarting`
- `resource.rebound`

### Device / driver events

Freeze these names:
- `device.unavailable`
- `driver.failed`
- `driver.restarted`
- `resource.withdrawn`

### Condition events

Freeze these names:
- `thermal.changed`
- `power.changed`

---

## Result / Error Vocabulary

### Outcomes

Stable top-level outcome classes:
- `ok`
- `error`

### Error reason classes

Freeze these names for broker-facing work:
- `invalid_action`
- `invalid_target`
- `invalid_arguments`
- `denied`
- `not_found`
- `not_supported`
- `conflict`
- `backend_failure`

---

## Record Vocabulary

Every meaningful action or job should conceptually allow:
- `spec`
- `status`
- `record`

Even if the first slice implements this only partially, these terms should remain stable.

For broker actions, a minimum record concept includes:
- action
- target
- origin
- outcome
- timestamp

---

## Vocabulary Discipline

The point of this file is not completeness.
The point is to stop accidental drift.

### Rules

- Do not invent alternate state names casually.
- Do not let process terms replace job terms.
- Do not treat driver failure as kernel failure in vocabulary.
- Do not collapse resources into hidden implementation details.
- Prefer extending this vocabulary over creating parallel terminology.

---

## Key Rule

v0 vocabulary should be **small, stable, and future-compatible**.
