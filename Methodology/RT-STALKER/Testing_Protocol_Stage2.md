# Stage 2: "Isolated Idle" (The Architectural Limit)

## 🔬 Conditions
*   **Deep Isolation:** Activation of advanced core isolation mechanisms. 
*   **Boot Parameters:** Includes `isolcpus` (removal from general scheduler), `nohz_full` (disabling system timer ticks), and `rcu_nocbs` (offloading RCU callbacks).
*   **Interrupt Affinity:** Forced migration of all hardware interrupts (Network, Storage, Timers) to dedicated "service" cores using the `irqaffinity` parameter.
*   **Load:** Zero synthetic load on the system.

## 🛠 Measurement Methodology
Verification of determinism in a **"Sterile Software Environment."** The measurement thread is pinned to an isolated core with the highest real-time priority (`SCHED_FIFO`, priority 99).

## 🎯 Object of Analysis
*   Determining the ultimate microarchitectural capabilities of the specific SoC in the absence of resource competition.
*   Evaluating the effectiveness of "core sterilization" from background OS tasks.

## 📈 Expected Results
The latency distribution range narrows to its physical minimum, visually forming a **"Needle Profile"** on the histogram. Target metrics:
1.  **Cortex-A73 / A76 cores:** Target latency of **4–7 μs**.
2.  **Cortex-A53 / A55 cores:** Target latency of **9–12 μs**.

## 📜 Scientific Justification
Verification of the hypothesis that a specific software configuration can completely nullify the OS's impact on latency. This allows the system to reach the **physical speed limit of the processor pipeline**, establishing the absolute "lower floor" of response time for each investigated architecture.

---
*Next: [Stage 3: Hardcore Stress (Determinism Resilience)](./Testing_Protocol_Stage3.md)*
