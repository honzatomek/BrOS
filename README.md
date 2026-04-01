# BrOS

**BrOS** is a broker-centric, AI-native operating system architecture.

It is designed around the idea that modern computing is no longer well described by processes, files, and syscalls alone. BrOS treats **objects, actions, jobs, resources, and events** as first-class architectural concepts. The kernel remains the execution substrate, but meaningful control is centered in a brokered control plane that makes system behavior explicit, inspectable, attributable, and safe to automate.

BrOS is not a Unix replacement built by nostalgia, and it is not an operating system with AI pasted on top. It is an attempt to define an operating system whose core abstractions match modern use:

- structured orchestration rather than ad hoc automation
- compute-aware job handling rather than process-only thinking
- explicit resource visibility rather than hidden scheduler state
- event-driven state transitions rather than implicit side effects
- AI, Lua, CLI tools, and native apps operating through one action model
- native-first architecture with POSIX compatibility later, not the reverse

## Why BrOS exists

Mainstream systems are still organized around abstractions that stabilized in a different era:

- processes as the main unit of work
- files as the main unit of state
- syscalls as the main control boundary
- shells as the primary automation interface

Those abstractions remain powerful, but they no longer capture the dominant realities of modern systems:

- heterogeneous compute
- GPU and accelerator pressure
- memory bandwidth contention
- AI as planner and operator
- background indexing, inference, rendering, simulation, and workflow jobs
- the need for auditability, rollback, and attributable change

BrOS responds by moving meaning upward into a coherent control plane.

## Core architectural shift

Traditional systems are centered on:

- processes
- files
- syscalls

BrOS is centered on:

- **objects** as state
- **actions** as intent
- **jobs** as structured work
- **resources** as allocatable and measurable capacity
- **events** as observable state transition

This is the core model from which the rest of the system follows.

## Architecture at a glance

```text
User / UI / CLI
        ↓
Orchestrators / Clients
(AI, Lua, later Python, native apps)
        ↓
Action packets + capabilities
        ↓
Broker
(policy, validation, routing, logging, audit, job submission)
        ↓
System services
(objects, jobs, resources, storage, network, device services)
        ↓
Kernel
(memory, scheduling primitives, interrupts, isolation, syscalls)
        ↓
Hardware / VM
```

And later, in parallel:

```text
POSIX apps
   ↓
POSIX compatibility layer
   ↓
Broker / services
   ↓
Kernel
```

## Design principles

### Broker-centric control plane

All meaningful actions go through the broker. There are no parallel semantic control paths that bypass the system model.

### Minimal kernel philosophy

The kernel is the execution plane. It provides memory management, scheduling primitives, interrupts, isolation, and low-level mechanisms. It is not the place for policy, AI behavior, high-level job semantics, or application logic.

### AI as orchestrator, not privileged actor

AI is a first-class orchestrator, but it is not above the architecture. It uses broker actions, runs under capabilities, and is fully logged.

### Jobs are first-class

Heavy or resource-sensitive work is expressed as jobs, not as raw processes with hidden meaning.

### Resources are explicit and queryable

CPU, memory, GPU, power, thermal capacity, and related system resources are broker-visible objects with inspectable state.

### Events are universal

Meaningful state transitions emit events. Failure becomes state, not catastrophe.

### Native first, POSIX second

The native BrOS model defines the architectural truth. POSIX exists later as a compatibility layer, not as the core identity of the system.

## What BrOS is trying to prove first

The first true architectural milestone is deliberately strict:

**boot -> shell -> broker -> native `ls`**

This slice proves all of the following together:

- the kernel can boot and host userspace
- a minimal `init` and shell exist
- the broker exists as a real control plane
- object operations happen through the broker
- native tools are thin broker frontends
- broker actions are inspectable and logged

That milestone matters more than feature count because it proves the architecture is real.

## Planned implementation direction

The current project direction is:

- **Rust-first** for kernel and core system components
- minimal assembly only where required
- native services above a deliberately small kernel
- native tools as broker frontends
- Lua first as embedded automation language
- Python later as a peer orchestrator
- versioned storage and durable records later in the stack
- POSIX compatibility after the native architecture is established

## License model

BrOS uses a split license model.

### Core

The architectural and implementation core of BrOS is intended to use the **Mozilla Public License 2.0 (MPL-2.0)**.

This is the part of the project where file-level copyleft is useful because it encourages contribution-back on modifications to the core while remaining practical for broader integration.

Examples of what belongs to the core include:

- kernel
- broker
- core services
- native system interfaces
- canonical architecture-defining components

### Non-core / surrounding project material

The remainder of the repository is intended to use the **Apache License 2.0**.

This is the more permissive layer intended for surrounding materials and supporting project assets where broad reuse is useful.

Examples may include:

- documentation outside the architectural core
- examples and demos
- auxiliary tooling
- supporting scripts
- development helpers
- non-core utilities

### Important note

This README describes the intended repository license split. The repository should also contain the corresponding license files and a clear directory-level or file-level mapping once the codebase structure is finalized.

A practical repository layout would typically include at least:

- `LICENSES/MPL-2.0.txt`
- `LICENSES/Apache-2.0.txt`
- a top-level note explaining which directories are governed by which license

## Repository layout

A likely early repository shape is:

```text
.
├── README.md
├── CONTRIBUTING.md
├── LICENSES/
├── docs/
│   └── canonical/
├── kernel/
├── broker/
├── services/
├── apps/
├── tools/
├── scripts/
├── posix/
└── experiments/
```

This exact structure may evolve, but the separation should remain clear enough to preserve the architectural boundaries and the license split.

## Installation and first run

BrOS is still an early architecture project. The installation path will evolve as the first bootable slices become real. For now, the expected setup flow should remain simple and reproducible.

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Install the expected host tooling

The exact dependencies will depend on the current stage of implementation, but the general build and test environment will likely include:

- Rust toolchain (`rustup`, `cargo`)
- `qemu-system-x86_64` or another agreed target emulator
- `make` or an equivalent task runner
- `llvm-tools` or target-specific binutils as needed
- platform packages required for boot image creation

On a typical Linux host, that means installing the Rust toolchain and QEMU first, then any target-specific build helpers used by the repository.

### 3. Build the current target

A typical early flow should look something like:

```bash
make build
```

or:

```bash
cargo build
```

The project should prefer one obvious entry point rather than many competing ways to produce a bootable image.

### 4. Run BrOS in QEMU

A typical early flow should look something like:

```bash
make run
```

or, if the project later exposes explicit runner commands:

```bash
qemu-system-x86_64 <machine-and-image-arguments>
```

The preferred goal is a one-command developer path that boots directly into the current milestone target.

### 5. Expected early outcome

The earliest meaningful success line is not a full desktop environment. It is much smaller and more important:

- kernel boots
- serial logging works
- `init` starts
- shell starts
- shell can call the broker
- broker can answer `ping`
- native `ls` can list objects through broker

That is the first point where the architecture becomes real.

## Documentation map

The canonical project documentation should be read in this order:

1. `01_VISION_AND_MISSION.md`
2. `02_FOUNDATION.md`
3. `03_DETAILED_REFERENCE.md`
4. `04_KERNEL_AND_SYSTEM_ARCHITECTURE.md`
5. `05_BROKER_AND_CONTROL_PLANE.md`
6. `06_CORE_MODELS.md`
7. `07_TRIGGERS_FLOWS_INVARIANTS_AND_SCENARIOS.md`
8. `08_FIRST_VERTICAL_SLICE.md`
9. `09_DEVELOPER_GUIDE.md`
10. `10_NATIVE_APPLICATIONS.md`
11. `11_STORAGE_VERSIONING_AND_HISTORY.md`
12. `12_POSIX_COMPATIBILITY.md`
13. `13_ROADMAP_AND_OUTLOOK.md`
14. `14_STATE_AND_EVENT_VOCABULARY.md`
15. `ai_assistant_code_of_conduct.md` (standalone governance document)

## Contribution posture

BrOS should be developed with architectural discipline.

That means:

- no direct kernel-bypass control paths
- no POSIX-first design drift
- no ad hoc APIs outside the broker model
- no accidental collapse of jobs back into mere process language
- no hidden scheduler-only truth for allocatable resources
- no feature expansion that breaks the first-slice architectural proof

Contributors should prefer end-to-end vertical slices and explicit invariants over broad speculative subsystem sprawl.

Please see `CONTRIBUTING.md` for contribution guidelines.

## Status

BrOS is currently in the architecture-definition and early implementation-planning stage.

The current priority is not breadth.
It is architectural honesty.

The project should first prove:

**boot -> shell -> broker -> native `ls`**

Everything else should grow from that without violating the model.
