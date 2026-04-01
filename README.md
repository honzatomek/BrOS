# BrOS

**BrOS** is a broker-centric, modern-oriented operating system project.

The project explores what an operating system could look like if it were built around explicit control, structured actions, first-class jobs, visible resources, and event-driven state, rather than treating processes, files, and syscalls as the only meaningful center of the machine.

BrOS is designed around these core ideas:

- a **broker-centric control plane**
- a **minimal kernel** that provides execution and isolation primitives rather than policy
- **objects, actions, jobs, resources, and events** as the main conceptual model
- **AI, Lua, CLI tools, and later Python** using the same action model
- **native-first architecture**, with POSIX compatibility later
- **inspectable and attributable behavior** rather than hidden control paths

## Project status

BrOS is currently at the architecture and early project-shaping stage.

There is not yet a completed implementation. The current work is focused on building a coherent conceptual foundation, preserving internal consistency across the documentation set, and preparing for the first real implementation slice.

That first slice is intentionally strict:

**boot -> shell -> broker -> native `ls`**

This is the first milestone because it proves the architecture rather than merely accumulating features.

## Why this project exists

Mainstream operating systems still carry assumptions from a world where the main abstractions were:

- processes
- files
- syscalls
- shells

Those abstractions remain powerful and worth preserving where they fit, but they do not fully describe how modern systems are used:

- compute is heterogeneous
- memory, bandwidth, and thermal constraints matter more
- AI is becoming planner and operator, not only a tool inside one application
- heavy work is often job-like, not just process-like
- meaningful system actions increasingly need to be inspectable, attributable, and governable

BrOS explores an operating system model where those realities are treated as first-class concerns from the beginning.

## Core model

BrOS is centered on:

- **objects** as inspectable state
- **actions** as explicit intent
- **jobs** as structured work
- **resources** as allocatable and measurable capacity
- **events** as observable state transition

This means:

- meaningful actions go through the broker
- the kernel remains narrow in role
- heavy work is modeled explicitly
- resources are visible rather than hidden in scheduler internals
- failure becomes state, not catastrophe

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
(validation, routing, policy, logging, audit)
        ↓
System services
(objects, jobs, resources, storage, networking, device services)
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

All meaningful actions go through the broker.

### Minimal kernel philosophy

The kernel is the execution plane, not the semantic center of the system.

### AI as orchestrator, not privileged exception

AI is part of the architecture, but not above it.

### Jobs are first-class

Heavy or resource-sensitive work should be modeled as jobs, not left implicit.

### Resources are explicit and queryable

CPU, memory, GPU, power, thermal capacity, and similar system resources should be broker-visible.

### Events are universal

Meaningful state changes should emit observable events.

### Native first, POSIX later

The native BrOS model defines the architectural truth. POSIX compatibility is important, but secondary.

## Current implementation direction

The current direction is:

- **Rust-first** for kernel and core system code
- minimal assembly only where needed
- native services above a deliberately small kernel
- native tools as broker frontends
- Lua first as built-in automation language
- Python later as a peer orchestrator
- versioned storage later in the stack
- POSIX compatibility after the native architecture is established

## Repository roadmap focus

The project should advance in disciplined vertical slices.

The first serious target is:

**boot -> shell -> broker -> native `ls`**

That proves:

- kernel boot
- userspace entry
- init and shell
- broker ingress and dispatch
- object operations through the broker
- native tools as thin broker frontends
- inspectable logging of meaningful actions

## Licensing approach

BrOS is intended to begin with a **simple, contributor-friendly copyleft model**.

### Code

The project codebase is intended to use the **Mozilla Public License 2.0 (MPL-2.0)**.

This is the current preferred starting point because it strikes a practical balance:

- the repository remains public and forkable
- contributions remain welcome
- modifications to MPL-covered files remain open when distributed
- the project does not start with unnecessarily heavy legal friction

This fits the current nature of BrOS well:

- a public learning project
- open to outside contributions
- interested in reciprocity rather than lock-down
- cautious about letting improvements to the shared code disappear permanently into closed downstream changes

### Contributions

BrOS is intended to use a **Developer Certificate of Origin (DCO)** workflow rather than a CLA at the start.

This keeps contributions lighter-weight and closer to the style used by many engineering-driven open projects.

See:

- `CONTRIBUTING.md`
- `DCO.md`

### Branding and identity

The code license is not the same thing as project naming or trademark policy.

BrOS may later add a separate naming or trademark policy if the project becomes established enough for that to matter. That can be done without changing the basic contribution model.

## Installation and early run outline

BrOS is still early, so the exact commands will evolve as implementation becomes real. For now, the setup path should stay simple and explicit.

### 1. Clone the repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Install expected host tools

The likely early host requirements are:

- Rust toolchain (`rustup`, `cargo`)
- QEMU, likely `qemu-system-x86_64`
- `make` or another agreed task runner
- target-specific binutils or LLVM tools as needed
- packages required for boot image creation

A typical Linux-host setup will therefore start with:

- Rust
- QEMU
- basic build tooling

### 3. Build the current target

A likely early flow will look something like:

```bash
make build
```

or:

```bash
cargo build
```

The exact command should eventually be stabilized and documented in the repo root.

### 4. Run under QEMU

A likely early run flow will look something like:

```bash
make run
```

or an equivalent scripted QEMU entry point.

The goal is to keep the project runnable with one clear command once the first bootable slice exists.

## Expected repository shape

A likely early structure is:

```text
.
├── README.md
├── CONTRIBUTING.md
├── DCO.md
├── LICENSE
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

This may evolve, but the architectural separation should remain visible in the repository structure.

## How to contribute

If you want to contribute, start with:

1. reading the canonical docs
2. understanding the broker-centric model
3. checking the first vertical slice
4. keeping proposals narrow and architecturally honest

Please read `CONTRIBUTING.md` before opening larger architectural proposals.

## Canonical documentation

The canonical documentation set defines the architecture in full and should be treated as the current source of design truth.

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

## Current intent

BrOS starts as a public hobby and learning project.

But it is also being documented in a way that keeps the long-term groundwork sane if it grows into something more serious. That means trying to make good architectural and governance choices early, without suffocating the project under unnecessary complexity before the first line of real implementation lands.
