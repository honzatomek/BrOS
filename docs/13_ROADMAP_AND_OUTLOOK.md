# 13. Roadmap and Outlook

## Table of Contents

1. Narrative Roadmap
2. First True Milestone
3. VM-First and VM-Heavy Usage
4. VPN and Remote Trust Boundaries
5. VM Scalability
6. Tensor and Accelerator Compute Support
7. What to Preserve Early
8. Tight Conclusion

## 1. Narrative Roadmap

The system evolves in stages:

1. Bootable Rust kernel
2. Memory, interrupts, and scheduler substrate
3. Userspace and syscalls
4. `init` and minimal shell
5. Broker v0
6. Native object and file model
7. Native core tools: `ls`, `cat`, `cp`, `mv`, `rm`
8. Native Rust editor
   - first minimal `ed`-like or tiny line editor if needed
   - then `kilo`-like full-screen editor
9. Lua integration
10. Job system
11. Networking
12. Versioned storage
13. AI orchestrator
14. POSIX layer
15. `Midnight Commander`, `Vim`, `Python`, `GCC`

## 2. First True Milestone

Boot → shell → broker → ls

This proves the architecture is real.

## 3. VM-First and VM-Heavy Usage

### 3.1 Observation

A large share of modern serious computer use already happens through:

- local VMs
- remote VMs
- cloud and development environments
- remote desktop or session layers
- VPN-protected network perimeters

So BrOS should not assume that one physical desktop machine is the only important environment.

### 3.2 Architectural fit

BrOS scales conceptually to VM-heavy usage very well.

Why:

- broker model is transport-friendly
- jobs are already semantic objects rather than raw local processes
- resources are already modeled abstractly
- native actions are already structured and inspectable
- distributed or remote execution can be added later without inventing a second conceptual model

### 3.3 Recommended principle

BrOS should treat:

- local hardware
- local VM resources
- remote resources later

as different backing scopes under one conceptual model, not different architectural universes.

### 3.4 Practical implication

Do not make early assumptions that:

- object identity is always tied to one physical disk
- jobs are always purely local
- resources are always bare-metal devices
- sessions are always on the console machine

## 4. VPN and Remote Trust Boundaries

### 4.1 Observation

Real-world use often places the machine inside:

- company VPN
- cluster fabric
- secured lab network
- remote management boundary

This matters because BrOS already centers around:

- capabilities
- brokered control
- auditability
- explicit actions

### 4.2 Architectural fit

This is a strength for BrOS.

A broker-centric system is naturally friendlier to remote trust boundaries than an ambient shell and syscall culture, because:

- actions are explicit
- authority can be scoped
- logging is native
- approval and policy can sit at the broker boundary

### 4.3 Recommended principle

Remote or VPN-mediated use should be treated as:

- another capability / policy / transport context

not:

- a separate architecture

### 4.4 Long-term implication

Later, the broker may need:

- transport-neutral action framing
- remote caller identity
- capability delegation
- stronger audit lineage
- federation between local and remote brokers

But none of that breaks the current model.

It extends it.

## 5. VM Scalability

### 5.1 Conceptual answer

Yes, BrOS should scale to:

- running as a guest VM
- hosting internal service VMs later
- coordinating remote or virtual resources later

### 5.2 Why this is plausible

Because BrOS is already built around:

- semantic jobs
- abstract resources
- service boundaries
- device and resource views rather than bare assumptions

This means a virtual GPU, virtual NIC, remote storage target, or virtualized accelerator can still fit the same object and resource model.

### 5.3 Important caution

Do not let VM-friendly turn into cloud-only.

BrOS should remain valid for:

- local workstation
- laptop
- lab machine
- VM guest
- future cluster or VPN-connected environment

## 6. Tensor and Accelerator Compute Support

### 6.1 Observation

If BrOS is serious about modern workloads, it must be friendly to:

- tensor computation
- accelerator-backed inference
- numerical kernels
- GPU and NPU-aware scheduling
- model execution pipelines

The existing architecture already points in this direction through:

- first-class jobs
- broker-visible resources
- compute-aware scheduling
- accelerator preference and policy

### 6.2 Architectural fit

Tensor work should fit naturally into BrOS.

Not by making tensor a kernel primitive, but by recognizing that:

- accelerator compute is a first-class job and resource concern
- tensor runtimes are services or backends
- accelerator devices are exposed through device and resource services
- jobs declare device preference, budgets, and constraints

### 6.3 Recommended principle

BrOS should be:

- tensor-friendly
- accelerator-aware
- runtime-agnostic at the core

Meaning the OS should not hardcode one ML runtime as the truth.

It should provide the resource, job, and orchestration substrate on which tensor runtimes can live well.

### 6.4 Good conceptual split

- kernel: execution and isolation substrate
- device services: GPU / NPU / accelerator mediation
- resource manager: compute capacity, memory, pressure, topology
- job system: placement, class, constraints, runtime control
- user and runtime layer: tensor engines, inference services, numerical backends

That is the right hierarchy.

## 7. What to Preserve Early

Even before full implementation, early code should avoid assumptions that would block these futures.

Preserve these principles:

- resource model must remain abstract enough for virtual and remote backing
- broker actions should not assume only local console use
- job model should not assume only CPU-bound local tasks
- driver and device separation should permit virtual devices too
- capability model should be suitable for delegated or remote contexts later

## 8. Tight Conclusion

### 8.1 VM and VPN

BrOS is conceptually scalable to VM-heavy and VPN-mediated environments.

In fact, the broker, capability, and action model is unusually well-suited to them.

### 8.2 Tensor compute

BrOS can and should support tensor-heavy workloads.

Not by putting tensor logic in the kernel, but by making accelerators, memory pressure, topology, and job semantics first-class above the kernel.

### 8.3 Practical recommendation

Do not implement these big systems now.

But do protect the early architecture from assumptions that would make them awkward later.
