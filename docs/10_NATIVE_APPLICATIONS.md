# 10. Native Applications Roadmap

## Purpose

This document outlines native applications that should be developed for the AI-native OS.
These are not mere utilities, but **validation points of the architecture**.

Native applications:
- use broker actions directly
- do not rely on POSIX layer
- define the long-term ecosystem identity

---

## 1. Core Native Applications (Early)

### 1.1 File Tools
- ls
- cat
- cp
- mv
- rm

Role:
- validate object model
- validate broker file actions

---

### 1.2 Native Editor (Rust)
Suggested path:
- minimal line editor (ed-like)
- full-screen editor (kilo-like)

Role:
- validate terminal handling
- validate native app model
- enable real usability early

---

### 1.3 System Shell
- minimal command execution
- later: scripting integration

Role:
- debugging
- system control

---

## 2. System Introspection Tools

### 2.1 htop-like Tool
Purpose:
- visualize processes and jobs
- CPU/GPU usage
- memory pressure

Extended idea:
- show broker actions
- show job states
- show resource contention

This could become much more powerful than htop.

---

### 2.2 ifconfig / ip Tool
Purpose:
- inspect network interfaces
- configure networking

Native extension:
- broker-based network objects
- structured output

---

### 2.3 ps / job viewer
Purpose:
- list processes AND jobs

Key difference:
- jobs are first-class
- processes are implementation detail

---

## 3. Communication Tools

### 3.1 sendmail / msmtp-like Tool
Purpose:
- send structured messages (email or system messages)

Native extension:
- broker.send_message()
- unified messaging abstraction

Potential:
- email
- notifications
- inter-process communication

---

### 3.2 log viewer
Purpose:
- inspect broker logs
- filter actions by actor / type

Native strength:
- system is fully auditable

---

## 4. Compute / Job Tools

### 4.1 jobctl
Purpose:
- submit jobs
- monitor jobs
- cancel / pause / resume

Analogy:
- simplified Slurm for local OS

---

### 4.2 resource monitor
Purpose:
- track CPU/GPU/memory allocation per job

---

## 5. Advanced Native Apps (Mid-Term)

### 5.1 File Manager (Midnight Commander-like)
Purpose:
- interactive navigation
- file operations

Role:
- validates full object + UI integration

---

### 5.2 System Dashboard
Purpose:
- unified view:
  - jobs
  - resources
  - logs
  - network

---

### 5.3 AI Console
Purpose:
- interact with AI orchestrator
- inspect planned actions
- approve/reject actions

---

## 6. Long-Term Native Applications

### 6.1 Workflow Engine
Purpose:
- define multi-step pipelines
- reusable workflows

---

### 6.2 Dataset / Model Manager
Purpose:
- manage large datasets
- manage AI models
- track versions

---

### 6.3 Snapshot Manager
Purpose:
- inspect system/object history
- restore versions

---

## 7. Design Philosophy

Native applications should:
- be thin over broker
- avoid duplicating logic
- expose structured actions
- remain replaceable

---

## 8. Key Insight

Native applications are not just tools.

They are:
- **proof of architecture**
- **interfaces to system capabilities**
- **building blocks for future ecosystem**

---

## Summary

Start with:
- file tools
- shell
- editor

Then expand to:
- introspection
- networking
- job control
- messaging

Then:
- full applications and UI
