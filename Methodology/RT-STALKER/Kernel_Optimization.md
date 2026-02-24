# Kernel Optimization: The "Surgical" Approach

To achieve minimal latency, simply applying the `PREEMPT_RT` patch is insufficient. The **RT-STALKER** protocol requires a deep, "surgical" optimization of kernel subsystems to eliminate all sources of non-deterministic behavior (Jitter).

## 🛠️ Key Optimization Blocks

### 1. Preemption & Scheduling
*   **Fully Preemptible Kernel (Real-Time):** Converts the kernel into a hard real-time mode where almost all interrupts and critical sections become preemptible.
*   **Removal of RT Throttling:** Disables CPU bandwidth provisioning and execution time limits for RT tasks (`SCHED_RR/FIFO`). This ensures 100% CPU resource access without forced pauses.

### 2. Timers Subsystem
*   **High-Resolution Timers (HRT):** Enabled for nanosecond precision.
*   **Full Tickless System (NO_HZ_FULL):** Implements a 1000 Hz poll rate combined with Full Dynticks. This stops the system tick on isolated cores, eliminating periodic jitter from timer interrupts.

### 3. CPU Power Management (CPU PM)
*   **Amputation of CPU Idle & PSCI:** Completely disables power-saving drivers and deep sleep states (**C-states**). This eliminates "wake-up" latencies (20–150 μs), ensuring instant reaction.
*   **Performance Governor:** Locks all cores at maximum frequency. Eliminates delays associated with voltage and frequency scaling (**DVFS**).

### 4. Memory Management (Determinism)
*   **Static Memory Profile:** Disables Memory Compaction, KSM (page sharing), and Transparent Hugepages.
*   **Page Allocator Optimization:** Removes page allocator randomization. Prevents the kernel from background RAM manipulations that cause unpredictable **page faults**.

### 5. Kernel Hardening & Security (The Efficiency Trade-off)
*   **Security Stripping:** Disables Spectre/Meltdown mitigations (**KPTI, KASLR**). This reduces system call execution time by 20–30% by eliminating constant page table switching.
*   **Removal of Tracers & BPF:** Completely strips the BPF subsystem, ftrace, and debugging mechanisms. Removing thousands of "NOP" instructions and internal checks creates a "straightened," predictable execution path.

### 6. Subsystem Clean-up
*   **Namespace Reduction:** Keeps only the bare minimum (UTS, IPC, PID) required for *systemd*. Networking and User namespaces are removed to simplify internal access right checks.
*   **RCU Priority Boosting:** Configures aggressive RCU priority boosting (latency reduced to 10ms). Ensures low-priority system tasks do not block resources needed for RT threads.

---
*Next: [Experimental Design & Testing Protocol](./Testing_Protocol.md)*
