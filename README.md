# Axiom Torque-Sync + Mirage AR 🏎️⚡🕶️

## Dual-Core Bare-Metal SimRacing Telemetry & Holographic Projection Engine
`#![no_std]` `#![no_main]` `architecture: arm-cortex-m7 / risc-v` `latency: 1.2ms`

---

## 01. ARCHITECTURE OVERVIEW

Axiom Torque-Sync + Mirage AR is a mission-critical, hardware-integrated ecosystem designed for ultra-low latency simulation and real-time holographic telemetry injection. Operating completely bare-metal without OS context switching, the framework executes deterministic control loops to synchronize magnetic direct-drive actuators with low-profile spatial augmented reality hardware.

By bypassing standard kernel layers and eliminating heap allocation pools (`#![no_std]`), the system achieves a persistent sub-1.2ms telemetry-to-projection pipeline. The architecture splits execution across two dedicated silicon cores:

* **Core 0 (Torque-Sync Core):** Governs direct-drive magnetic force feedback, managing phase-current computations and high-frequency torque vectors at 40kHz.
* **Core 1 (Mirage Core):** Handles deterministic coordinate mapping, asynchronous data streaming, and laser/micro-LED projection matrices directly onto the physical environment.

## 02. HARDWARE MEMORY MAP & DIRECT REGISTRY MAPPING (MMIO)
To achieve deterministic sub-1.2ms execution execution execution without an underlying kernel, the firmware interacts directly with the silicon through Memory-Mapped I/O (MMIO). The memory space is segregated to prevent jitter and cache-misses between the Torque-Sync actuator loops and the Mirage AR projection matrix.
### Memory Boundary Allocations
* `0x4000_0000 - 0x4000_3FFF`: Core 0 - High-Speed PWM Current Controller (Torque-Sync Feedback)
* `0x4000_4000 - 0x4000_7FFF`: Core 0 - Quadrature Encoder Interface (QEI) High-Resolution Telemetry
* `0x4001_0000 - 0x4001_FFFF`: Core 1 - High-Refresh Laser/Micro-LED Projection Framebuffer
* `0x4002_0000 - 0x4002_3FFF`: Core 1 - Ultra-Low Latency SPI IMU Interconnect (Head Tracking)
### Critical Hardware Registers

| Register Name | Memory Address | Bits | Function / Description |
| :--- | :--- | :--- | :--- |
| `TS_CTRL` | `0x4000_0000` | 32 | Torque-Sync Master Enable, Phase Injection Mode, Safety Cutoff |
| `TS_VEC_I` | `0x4000_0004` | 32 | Current Vector I-Phase Target (Direct Drive Magnetic Force 16-bit) |
| `TS_VEC_Q` | `0x4000_0008` | 32 | Current Vector Q-Phase Target (Direct Drive Magnetic Force 16-bit) |
| `TS_TELE_POS` | `0x4000_4000` | 32 | Raw Wheel Position Telemetry (32-bit absolute optical encoder) |
| `MRG_LAS_CTRL` | `0x4001_0000` | 32 | Mirage Projector Driver Control (Laser modulation & frequency clock) |
| `MRG_X_COORD` | `0x4001_0004` | 16 | Holographic Target X Coordinate (Normalized Space) |
| `MRG_Y_COORD` | `0x4001_0006` | 16 | Holographic Target Y Coordinate (Normalized Space) |
| `MRG_IMU_DATA` | `0x4002_0000` | 128 | Asynchronous Spatial 6-DoF Head-Tracking Vector (Raw Stream) |


