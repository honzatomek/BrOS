# 04. Kernel and System Architecture

## Table of Contents

1. Purpose
2. Architectural Alignment
3. Kernel Philosophy
4. Kernel Responsibilities
5. Why the Kernel Stays Small
6. Driver Boundary
7. Broker Placement in the Stack
8. AI Integration
9. Layer Responsibilities
10. System Summary Rule

## 1. Purpose

This document defines the structural architecture of BrOS at the level of execution substrate, control plane placement, service placement, and orchestrator alignment.

## 2. Architectural Alignment

BrOS is defined by:

- broker-centric control plane
- action-based system interface
- compute-aware job scheduling
- versioned storage
- Rust-first kernel
- Lua, Python, and AI orchestrators

All design decisions must align with this architecture.

If a suggestion conflicts with the broker-centric architecture, it must be rejected.

Do not propose:

- direct kernel bypass
- POSIX-first solutions
- ad-hoc APIs outside broker

## 3. Kernel Philosophy

The kernel is deliberately minimal.

It is not the place for:

- policy
- AI
- business logic

The kernel is the execution substrate.

## 4. Kernel Responsibilities

The kernel is responsible for:

- memory management
- scheduling
- interrupt handling
- syscall interface

More broadly, it provides:

- execution
- isolation
- primitives

It is the layer that makes safe and efficient execution possible.

## 5. Why the Kernel Stays Small

Keeping the kernel small:

- reduces risk
- improves maintainability
- allows rapid evolution above the kernel

The project is Rust-first because that aligns with:

- safety
- long-term maintainability
- avoidance of starting in C and carrying legacy forever

## 6. Driver Boundary

The kernel does not contain most drivers.

The intended direction is:

- drivers run as isolated device services by default
- driver failure is recoverable
- kernel crash leads to reboot
- driver crash should be contained

Stated directly:

Kernel crash = reboot  
Driver crash = recoverable

This boundary matters because BrOS treats resource and device failure as modeled runtime state rather than automatic whole-system collapse whenever feasible.

## 7. Broker Placement in the Stack

The broker is the semantic core of the system.

It mediates all meaningful actions and sits above the kernel, alongside system services, as the control plane of the machine.

Traditional systems fragment control across:

- syscalls
- shells
- application APIs

The broker unifies this into one coherent control model.

## 8. AI Integration

### 8.1 Role of AI

AI is an orchestrator, not a privileged entity.

### 8.2 Rules

- AI must use the broker.
- AI cannot bypass the system.
- AI operates under capabilities.
- All AI actions are logged.

### 8.3 Why This Works

This avoids:

- unsafe automation
- hidden actions
- unpredictability

### 8.4 Long-Term Direction

The long-term direction includes:

- job-aware AI
- adaptive behavior
- controlled and inspectable operation

## 9. Layer Responsibilities

### 9.1 User / UI / CLI

Human-facing interaction surfaces and workflow entry points.

### 9.2 Orchestrators

- AI
- Lua
- Python later
- CLI tools
- native apps

All orchestrators are clients of the broker model.

### 9.3 Broker

Responsibilities:

- validate action packets
- enforce capabilities
- route to services
- log actions
- apply policy

### 9.4 Services

Services implement the substantive subsystems above the kernel, including:

- object and file backends
- job services
- resource services
- storage services
- networking services
- device-backed resource views

### 9.5 Kernel

Responsibilities remain limited to execution, isolation, memory, interrupts, and syscall substrate.

## 10. System Summary Rule

Same verbs, different authority.

AI, Lua, Python, and CLI all use identical actions but operate under different capabilities.

That is the central architectural split between orchestration and authority.
