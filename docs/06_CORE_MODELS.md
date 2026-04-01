# 06. Core Models — Jobs, Resources, Constraints, Events, and Scheduling

## Table of Contents

1. Purpose
2. Job System
3. Resource and Constraint Model
4. Event Model
5. Device and Resource Failure Model
6. Scheduling Principle
7. Runtime Guarantees
8. Record Structure
9. Core Rule

## 1. Purpose

This document gathers the core semantic models that operate above the kernel and beneath orchestrators: jobs, resources, constraints, events, and the runtime scheduling truths that connect them.

## 2. Job System

### 2.1 Core Principle

Job = intentful schedulable object  
Process = execution mechanism

### 2.2 Job Structure

A job consists of:

- spec
- status
- record

#### Spec

The intended work.

#### Status

The current runtime state.

#### Record

The attributable history of the job.

### 2.3 Resource Model Within a Job

Jobs interact with resources through:

- request
- limits
- grant
- usage

### 2.4 Constraints

Jobs may carry constraints of these kinds:

- hard
- soft
- dynamic
- relational

### 2.5 Scheduling Classes

Jobs may be classified as:

- interactive
- responsive
- background
- batch
- maintenance
- reservation

### 2.6 Lifecycle

The core lifecycle is:

`draft → submitted → validated → queued → admitted → running → completed/failed`

The broader reserved state model is richer and is frozen separately in the vocabulary document.

### 2.7 Phases

Jobs have at least two major phases:

1. admission
2. runtime control

Admission decides whether work may begin under current conditions.

Runtime control reacts to changing conditions while work is already in progress.

### 2.8 New and Extended Runtime States

The broader job model also reserves:

- degraded
- throttled
- paused
- checkpointing

These are important because BrOS does not assume that runtime conditions remain static after admission.

## 3. Resource and Constraint Model

### 3.1 Principle

All allocatable resources are broker-visible objects.

### 3.2 Resource Object

Each resource exposes:

- inventory
- capacity
- allocation
- usage
- pressure
- policy

### 3.3 Resource Names

Representative resource identities include:

- `resource://cpu`
- `resource://memory`
- `resource://gpu/0`
- `resource://thermal/cpu`
- `resource://power/system`

### 3.4 Query Model

The model includes:

- `resource.list`
- `resource.query`
- `resource.allocations`
- `resource.topology`

### 3.5 Watch Model

Resources support:

- `resource.watch`
- event-driven updates

### 3.6 Constraint Types

Constraint types are:

- hard
- soft
- dynamic
- relational

### 3.7 Rule

No allocation without queryable state.

### 3.8 Backing Model

Resources are produced by device services.

### 3.9 Resource States

Resources may be:

- available
- degraded
- unavailable
- restarting

### 3.10 Resource Failure Rule

Resource failure is expected and recoverable.

## 4. Event Model

### 4.1 Principle

All meaningful state transitions emit events.

### 4.2 Job Events

Representative job events include:

- submitted
- validated
- queued
- admitted
- running
- paused
- resumed
- completed
- failed

### 4.3 Resource Events

Representative resource events include:

- capacity_changed
- pressure_changed
- allocation_changed
- thermal_changed
- power_changed

### 4.4 Usage

The model is:

- subscription-based
- broker-delivered

### 4.5 Device and Resource Events

Additional event categories include:

- `device_unavailable`
- `driver_failed`
- `driver_restarted`
- `resource_withdrawn`
- `resource_rebound`

### 4.6 Principle

All hardware failures are event-driven.

## 5. Device and Resource Failure Model

Driver and device failure should be modeled as runtime state whenever feasible.

That means:

- device unavailability is observable
- resource withdrawal is a valid condition
- jobs may degrade, pause, fail explicitly, or wait for recovery
- the rest of the machine should continue when the failure is containable

This is one of the strongest distinctions between BrOS and designs that treat such failures as semantically opaque.

## 6. Scheduling Principle

Scheduling is based on:

- job intent
- resource state
- constraints
- policy

### 6.1 Key Rule

Admission and runtime decisions must use broker-visible resource state.

### 6.2 Admission vs Runtime

Admission is not runtime.

A job can be admitted correctly and still require later adaptation under:

- pressure changes
- power changes
- thermal changes
- device loss
- driver restart
- resource withdrawal

## 7. Runtime Guarantees

Jobs must handle:

- resource loss
- device disappearance
- driver restart

Runtime control reacts to resource and device events.

This is not an exceptional afterthought. It is part of the system model.

## 8. Record Structure

Every meaningful action or job should conceptually allow:

- spec
- status
- record

For jobs, the record is the durable explanation of what happened over time.

For broker-facing work, a minimum record concept includes:

- action
- target
- origin
- outcome
- timestamp

## 9. Core Rule

Heavy compute is first-class, runtime conditions are expected to change, and meaningful change must remain explicit through broker-visible state and events.
