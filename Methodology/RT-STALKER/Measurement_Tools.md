# Measurement Tools & Algorithmic Validation

To achieve objective results, the **RT-STALKER** protocol utilizes specialized tools designed to detect the worst-case scenarios of system behavior.

## 🛠️ The Primary Tool: `cyclictest`

The core measurement utility is **cyclictest**, part of the authoritative `rt-tests` suite. Developed in the mid-2000s by the creators of the **PREEMPT_RT** patch (**Thomas Gleixner** and **Ingo Molnar**), this tool was specifically designed to identify **Worst-Case Latency** rather than average performance.

### 🔬 Core Mechanisms
*   **`clock_nanosleep`:** Utilized by default as the most deterministic method for triggering delays.
*   **`--mlockall`:** A critical flag that locks the test process into RAM, preventing the virtual memory subsystem (paging/swapping) from distorting results.

### 🧠 The "Alarm Clock" Analogy
The algorithm operates like a high-precision alarm clock verifying if a person wakes up exactly on time:
1.  **Registration:** The utility creates a high-priority thread and pins it to a specific CPU core (**affinity**).
2.  **Sleep:** The thread instructs the kernel: "Wake me up in exactly **T** microseconds."
3.  **Wake-up:** When time **T** arrives, the kernel sends a signal, and the thread resumes.
4.  **Measurement:** Immediately upon waking, the thread checks the system clock (`clock_gettime`) and compares:
    *   **T_actual:** The time it actually woke up.
    *   **T_target:** The time it was supposed to wake up.
5.  **Calculation:** Latency is calculated as `Δ = T_actual - T_target`.
6.  **Cycle:** The thread repeats this process millions of times to capture every possible spike (jitter).

*Example: An **Avg: 8 μs** result means the kernel spends only 8 microseconds to stop the current task, save its state, and launch the cyclictest thread.*

---

## ⚡ The "Hammer": `stress-ng`

To verify resilience (Stage 3), we use **stress-ng** — the industry-standard tool for exhausting system resources.

### 🏗️ Stress Algorithm
Unlike simple benchmarks, `stress-ng` acts as a **"Noisy Neighbor"** by saturating hardware pathways:
*   **CPU Stress:** Constant computation to keep execution units busy.
*   **Cache/RAM Attack:** Utilizing `mmap` and `vm` workers to force cache thrashing and memory bus saturation.
*   **I/O Pressure:** Continuous read/write operations to flood the system's internal interconnects.

By tasksetting `stress-ng` to "service" cores while `cyclictest` runs on "isolated" cores, we force the SoC's **internal hardware arbitration** to show its true face under maximum pressure.

---
*Next: [Experimental Setup & Hardware Platforms](./Hardware_Setup.md)*
