# RT-STALKER: Testing Protocol & Methodology

The **RT-STALKER** protocol is divided into three strictly defined stages. This phased approach allows for the isolation of software-induced factors from the inherent hardware constraints of the SoC.

## Stage 1: "Vanilla Baseline" (Control Group)

### 🔬 Conditions
*   **System State:** Standard OS boot with the Linux 6.x-RT kernel. 
*   **Config:** Core isolation (`isolcpus`), interrupt affinity (`irqaffinity`), and advanced scheduler parameters are **deactivated**.
*   **Load:** Zero synthetic load on the system.

### 🛠 Measurement Methodology
This stage simulates a standard control application running in a Linux RT environment without specific OS optimizations. Test threads have no **CPU Affinity** and are free to be moved by the scheduler across all available computing resources.

### 🎯 Object of Analysis
*   Dynamics of the task scheduler in its stock state.
*   Evaluation of the impact of **inter-core migrations** on determinism.
*   Special focus on competition for the cache hierarchy (**L2/L3**) during context switching between performance (**big**) and energy-efficient (**LITTLE**) clusters.

### 📈 Expected Results
A highly unstable latency profile (the **"Comb Effect"**) with jitter ranging from **20 to 150 μs**. The primary contributors to this latency are:
1.  **C-states:** Micro-pauses occurring when cores exit deep sleep states.
2.  **Cache Locality:** Time lost on invalidation and refilling of cache lines when a thread "jumps" between cores with different architectures.

### 📜 Scientific Justification
This stage provides experimental confirmation that the integration of the **PREEMPT_RT** patch is merely a foundation, not a guarantee of industrial-grade determinism. Without systemic tuning, the heterogeneous nature of modern SoCs negates the advantages of an RT kernel.

---
*Next: [Stage 2: Isolated Idle (Architectural Limit)](./Testing_Protocol_Stage2.md)*
