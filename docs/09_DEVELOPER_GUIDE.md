# 09. Developer Guide and Contribution Posture

## Table of Contents

1. Purpose
2. How to Approach the Project
3. Development Philosophy
4. First Goal
5. Contribution and Development Posture
6. Entry Experience
7. Practical Project Principles
8. Contribution Rules
9. Design Discipline Questions
10. Zero-Friction Goal

## 1. Purpose

This document defines how BrOS should be developed so that the architecture remains visible, testable, and contribution-friendly from the beginning.

## 2. How to Approach the Project

1. Do not overengineer early.
2. Keep the kernel minimal.
3. Focus on broker API clarity.
4. Build vertical slices end to end.

## 3. Development Philosophy

Development should follow these principles:

- prefer correctness over features
- avoid legacy decisions early
- keep the system inspectable

## 4. First Goal

Implement:

- kernel boot
- minimal shell
- broker call
- `ls` via broker

## 5. Contribution and Development Posture

BrOS should be contribution-friendly from the beginning, not after the architecture is already buried under complexity.

This requires more than good intentions. It requires an explicit development posture.

## 6. Entry Experience

A contributor should be able to:

1. clone the repository
2. install a small, explicit toolchain
3. build the system
4. run it in QEMU
5. inspect what the broker is doing
6. understand where a change belongs architecturally

This should not require large hidden infrastructure, private setup knowledge, or broad unrelated dependencies.

## 7. Practical Project Principles

The project should prefer:

- minimal overhead
- little dependency burden
- local reproducibility
- explicit setup
- small trusted core
- architecture visible early in code
- end-to-end vertical slices over sprawling partial subsystems

Why this matters:

A project with ambitious architecture can easily become inaccessible if the development environment grows opaque or heavyweight. BrOS should not become easier to describe than to build.

Ease of entry is therefore part of the architecture’s practical credibility.

## 8. Contribution Rules

Contributors should reject changes that:

- bypass the broker for meaningful system behavior
- turn native tools into private semantic authorities
- move policy or semantic control into the kernel
- introduce ad hoc APIs outside the broker for meaningful actions
- let the POSIX layer become the de facto truth of the system
- create hidden state or hidden control behavior that the architecture cannot inspect

## 9. Design Discipline Questions

Every contribution should be able to answer:

- which layer does this belong to?
- should this be a broker action?
- does this create hidden control or hidden state?
- does this preserve native-first architecture?
- does this make future job, resource, and event integration easier or harder?
- does this accidentally harden the wrong model in an early phase?

If these questions cannot be answered clearly, the design likely needs more work.

## 10. Zero-Friction Goal

BrOS should remain:

- buildable in minutes
- understandable in hours
- modifiable in days

This is not a literal guarantee in all cases. It is a contribution health target that keeps the project honest.
