# Experimental Setup: Build Infrastructure & Target SoC

A truly deterministic system begins with a controlled build process from source, rather than pre-built images. This ensures that every architectural optimization is baked directly into the binary.

## 🏗️ Build Infrastructure: The "Native Station"

For our experiments, we utilized a **Native Build** strategy (ARM-on-ARM). By compiling directly on the target architecture, we eliminate cross-compilation artifacts and allow for maximum optimization using the specific instruction sets of the target CPUs.

### 🏭 The "Factory" Node: Orange Pi 5B
To handle the heavy parallel compilation of the 6.18-RT kernels, we deployed a high-performance build station:
*   **Hardware:** Rockchip RK3588 (8-core ARMv8). This allows us to reduce a full kernel build cycle to **60–90 minutes**.
*   **Host OS:** Orange Pi Linux (Kernel 6.1.31) — a stable base with full hardware acceleration.
*   **Guest OS (Containerization):** **Ubuntu 24.04 LTS**. The build environment is isolated, ensuring access to the latest toolchains (**GCC 13+**) and libraries required for the 6.18-RT series.

### 🛠️ The Framework: Armbian Builder
We chose **Armbian Builder** as our primary orchestration tool. This framework automates:
1.  **RT-Patching:** Systematic application of the `PREEMPT_RT` patchset.
2.  **Surgical Config:** Deep kernel tuning via the `menuconfig` interface.
3.  **Artifact Generation:** Consistent building of `.deb` packages and **DTB (Device Tree Blobs)** for various SoCs.

---

## 🔬 Target Architectures: 5 Years of ARM Evolution

Our build environment is designed to generate optimized images for a wide spectrum of architectures, covering the evolution of ARM processors over the last 5 years:


| SoC | Board | Architecture (Cores) | Role in Research |
| :--- | :--- | :--- | :--- |
| **Broadcom BCM2710** | RPi Zero 2 W | 4x Cortex-A53 (1.0 GHz) | Classical A53 Baseline |
| **Allwinner H618** | Orange Pi Zero 2W | 4x Cortex-A53 (1.5 GHz) | Overclocked Budget Entry |
| **Rockchip RK3566** | Radxa Zero 3 | 4x Cortex-A55 (1.8 GHz) | Modern Energy-Efficient Standard |
| **Amlogic A311D** | Radxa Zero 2 Pro | 4x A73 (2.2) + 2x A53 (1.8) | Heterogeneous big.LITTLE Study |
| **Allwinner A733** | Radxa Cube Zero A7Z | **2x A76 (2.0)** + 6x A55 | **High-Performance Flagship (Record)** |

---

### 📜 Scientific Rigor
This systemic approach guarantees that every tested kernel carries an **identical set of RT-optimizations**. This ensures that our comparative architectural study remains objective, isolating the physical performance of the silicon from software configuration variances.

---
*Next: [Experimental Design](./Experimental_Design.md)*
