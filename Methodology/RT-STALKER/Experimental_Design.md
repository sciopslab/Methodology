# Experimental Design: Implementation of RT-STALKER

To validate the **RT-STALKER** protocol, we executed a three-stage experimental design. This structure allows us to separate software overhead from the inherent silicon-level performance of each SoC.

## 🧪 Stage 1: "Vanilla Baseline" (Identifying Hardware Potential)
**Objective:** Measure latencies on a "stock" RT kernel without specific isolation tuning.

*   **Configuration:** Standard boot of the 6.x-RT kernel. All `isolcpus` and `irqaffinity` mechanisms are **deactivated**.
*   **Procedure:** Running `cyclictest` in SMP mode across all available cores.
    ```bash
    cyclictest -m -p 99 -t {All_Cores} -i 200 -h 100 -l 1000000
    ```
*   **Object of Analysis:** Impact of context switching between clusters (**big.LITTLE**) and delays from exiting sleep states (**C-states**). This stage captures the initial jitter and the **"Comb Effect."**

---

## 🔬 Stage 2: "Isolated Idle" (Reaching the Architectural Limit)
**Objective:** Evaluate pure microarchitecture through complete software sterilization of the target cores.

*   **Kernel Surgery (Boot Parameters):**
    ```bash
    isolcpus={RT_Cores} nohz_full={RT_Cores} rcu_nocbs={RT_Cores} irqaffinity={Service_Cores} cpuidle.off=1 processor.max_cstate=0
    ```
*   **Procedure:** Running measurements with strict **CPU Affinity** tied to the sterilized cores.
    ```bash
    sudo cyclictest -m -t {RT_Count} -a {RT_Cores} -p 99 -i 200 -l 1000000
    ```
*   **Object of Analysis:** Establishing the absolute **"Lower Floor"** of response time. This stage is where the **2 μs record** was captured on the Radxa Cube (Cortex-A76).

---

## ⚡ Stage 3: "Hardcore Stress" (Determinism Resilience)
**Objective:** Verify system immunity to resource degradation in **"Noisy Neighbor"** conditions.

*   **Configuration:** Preservation of Stage 2 isolation + extreme synthetic load on "service" cores.
*   **Stress Vector (Load Generation):**
    ```bash
    sudo stress-ng --cpu {Service_Count} --taskset {Service_Cores} --cache 4 --vm 4 --mmap 4 --switch 4 --timeout 1h &
    ```
*   **Procedure:** The measurement thread remains on the isolated core, competing for the shared system bus and L3 cache with the attack processes.
*   **Hard RT Criterion:** If **Max Latency remains < 70 μs** under these conditions, the platform is certified for critical control loops.

---

## Hard Real-Time (Hard RT) Criterion
**For the purposes of this research, an industrial evaluation standard has been established:**
*   **The Threshold:** If, under extreme load conditions ("Stage 3: Hardcore Stress"), the Max Latency does not exceed the 70-microsecond (70 μs) limit, the system is certified as suitable for Hard Real-Time tasks.
*   **The Penalty:** Any excursion beyond this threshold is classified as a loss of determinism, which is unacceptable for critical control loops and mission-critical automated systems.


*Next: [Comparative Results & The 2μs Record](./Results.md)*


