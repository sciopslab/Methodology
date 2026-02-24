# Stage 3: "Hardcore Stress" (Determinism Resilience)

## 🔬 Conditions
*   **Persistent Isolation:** Preservation of the full resource isolation configuration from Stage 2.
*   **Extreme Synthetic Load:** Parallel to precision measurements, a massive load is applied to non-isolated ("service") cores using the `stress-ng` utility.
*   **Combined Attack Vector:** Simultaneous saturation of **CPU, Cache, and RAM** (via `mmap` and `vm` mechanisms) along with the **I/O subsystem**. 
*   **Utilization:** The service cluster is driven to **100% resource utilization**.

## 🛠 Measurement Methodology
Evaluation of determinism under **"Noisy Neighbor"** conditions. The measurement thread continues operation on the isolated core with the highest real-time priority, competing for shared system resources (**L3 cache and memory bus**) with the aggressive background processes.

## 🎯 Object of Analysis
*   Resilience of isolated cores to the degradation of system bus bandwidth.
*   Verification of the effectiveness of **RCU Priority Boosting** mechanisms.
*   Analyzing the scheduler's ability to completely shield the RT thread from background "noise."

## 📈 Expected Results
Maintenance of the structural integrity of the latency profile. 
1.  **Avg Deviation:** Average latency should not deviate from Stage 2 results by more than **1–3 μs**.
2.  **Max Latency:** Peak spikes must remain within the industrial **Hard RT standard (<70 μs)**, despite critical saturation of the SoC's hardware resources.

## 📜 Scientific Justification
Experimental proof that the hardware-software synergy (**Optimized Kernel + Core Isolation**) creates **immunity to external computational loads**. This stage is the "killer feature" of the research, demonstrating the superiority of deep architectural tuning over the generic use of RT patches.

---
*Next: [Measurement Tools & Experimental Setup](./Experimental_Setup.md)*
