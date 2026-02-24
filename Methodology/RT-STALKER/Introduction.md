# Part 1: Introduction

## 🛰️ The Era of Zero-Platforms and Mainline RT

Since 2020, single-board computer (SBC) manufacturers have increasingly pivoted toward radical miniaturization. Minimum dimensions, low weight, and high performance comparable to desktop PCs have become the dominant trends in mobile and embedded technology. Today, the market is saturated with **"Zero-class" platforms** — Systems-on-Chip (SoC) from the silicon titans of both East and West.

On the software front, a milestone was reached in early December 2025: **Linux 6.18** was officially designated as a Long-Term Support (LTS) release, with maintenance guaranteed until December 2027. This kernel version introduces several critical shifts:

*   **~40%** of all changes are dedicated to device drivers;
*   **~16%** involve updates to architecture-specific code;
*   **~12%** relate to the networking stack;
*   **~5%** concern file systems;
*   **~3%** target internal kernel subsystems.

### 🔬 The Real-Time Challenge

Despite these advancements, the standard Linux kernel possesses inherent flaws when tasked with **Hard Real-Time** operations. For instance, "soft interrupts" (softirqs) traditionally execute in the context of the interrupted process, creating massive, uncontrollable latencies.

The fact that 40% of changes involve drivers is no coincidence. A primary objective of the modern kernel is to eliminate the historical dependency on the external **PREEMPT_RT** patch. The integrated RT logic now handles these challenges by:

1.  **Threaded Interrupts:** Moving almost all *softirqs* into specialized system threads (kernel threads).
2.  **Preemption:** Enabling the preemption of these threads to prioritize high-priority RT processes.
3.  **RT-Mutexes:** Synchronizing critical sections using real-time mutexes instead of simple spinlocks.

---

## 🏛️ Scientific Relevance and Problem Statement

By default, the modern Linux kernel now operates in **Low Latency** mode. Hard Real-Time capabilities are no longer integrated via external patching but are optionally selected directly within the **General Setup** menu. This shift signals a fundamental change in the development vector starting from late 2025: a move toward **Hard Real-Time (Hard RT)** systems.

Hard RT systems are defined by the absolute necessity of meeting strict timing constraints. Failure to adhere to these limits can lead to catastrophic consequences—ranging from system failure and hardware damage to direct threats to human safety. The scientific relevance of this research is driven by the critical nature of these applications.

### ⚠️ The Core Problem
The primary challenge in the current embedded SoC market is the **absence of a unified, evidence-based approach** to evaluating their suitability for Hard RT tasks. Existing benchmarks often treat the "OS as a whole" (a black box), ignoring architectural heterogeneity (e.g., **big.LITTLE**) and the influence of deep kernel subsystems. This creates "informational noise," preventing engineers from objectively determining if a specific SoC is fit for a critical control loop.

### 🦅 Scientific Novelty: The RT-STALKER Protocol
Unlike standard synthetic tests, the proposed **RT-STALKER (Layered Architectural Latency Sterilization Protocol)** is based on the principle of phased software environment sterilization. 

**The novelty of this approach lies in:**
1.  **Systematic Sterilization:** Controlling the system's transition to a state of **"Bare-Metal under OS control,"** capturing the pure architectural latency limit.
2.  **Architectural Correlation:** Establishing a strict correlation between the microarchitecture (**Cortex-A53/55 vs. A73/76**) and the resulting system determinism under extreme synthetic loads.

---
*Next: [Methodology and Experimental Design](./Methodology_Design.md)*
