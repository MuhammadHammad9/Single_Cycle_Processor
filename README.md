# Single_Cycle_Processor
# RISC-V Single-Cycle CPU Simulation

![Status](https://img.shields.io/badge/Status-Verified-success)
![Language](https://img.shields.io/badge/Language-Verilog%20%7C%20SystemVerilog-blue)
![ISA](https://img.shields.io/badge/ISA-RISC--V-orange)

**Developed by Students of Ghulam Ishaq Khan (GIK) Institute.**

## 📖 Overview
This repository contains the design and simulation of a **32-bit Single-Cycle RISC-V Processor**. The project implements a subset of the RISC-V Instruction Set Architecture (ISA), focusing on the correct execution of instructions within a single clock cycle.

The design features a modular architecture including a Program Counter, Register File, ALU, Control Unit, and Memory subsystems.

## 🎯 Objectives
The primary goal of this project is to validate the data path and control logic for:
* **Data Path Integrity:** correct data movement between registers, ALU, and memory.
* **Control Logic:** Accurate generation of signals (`RegWrite`, `MemWrite`, `MemToReg`, etc.).
* **Instruction Support:** Handling of **I-type**, **R-type**, and **S-type** instructions.

---

## 🏗️ Architecture
The CPU is composed of distinct Verilog modules connected via a top-level wrapper.

| Module | Description |
| :--- | :--- |
| **Instruction Memory** | Stores program instructions; outputs 32-bit instruction based on PC input. |
| **Program Counter** | Register that tracks instruction address; updates on clock edge. |
| **Register File** | 32 general-purpose registers (`x0`–`x31`). Dual-read, single-write capability. |
| **ALU** | Performs arithmetic (ADD, SUB) and logic operations. |
| **Data Memory** | Simulates RAM. Handles `LW` (Load Word) and `SW` (Store Word). |
| **Control Unit** | Decodes the 7-bit opcode to generate control signals for the datapath. |

---

## 💻 Test Assembly Program
The processor was verified using the following 5-instruction assembly program. This sequence tests arithmetic, immediate values, and memory read/write operations.

| Step | Address | Instruction | Machine Code | Description |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `0x00` | `addi x7, x0, 2` | `00200393` | Adds 2 to x0; stores in **x7**. |
| 2 | `0x04` | `addi x6, x0, 0x123` | `12300313` | Adds 0x123 (291) to x0; stores in **x6**. |
| 3 | `0x08` | `sw x7, 0(x6)` | `00732023` | Stores val of x7 into memory at address **x6**. |
| 4 | `0x0C` | `lw x8, 0(x6)` | `00032403` | Loads memory from address x6 into **x8**. |
| 5 | `0x10` | `add x9, x8, x7` | `007404b3` | Adds x8 + x7; stores in **x9**. |

---

## ⚡ Simulation Results
The simulation confirms cycle-accurate execution.

| Cycle | Instruction | Operation | Result |
| :--- | :--- | :--- | :--- |
| **1** | `addi` | Fetch & Decode | `x7 = 2` |
| **2** | `addi` | Fetch & Decode | `x6 = 0x123` (291) |
| **3** | `sw` | Execute & Mem Write | `Mem[0x123] = 2` |
| **4** | `lw` | Execute & Mem Read | `x8 = 2` |
| **5** | `add` | Execute & Write Back | **`x9 = 4`** |

> **Success Criteria:** The final register value `x9` contains the value `4`, confirming that data was successfully stored to memory, retrieved, and processed by the ALU.

---

## 🚀 How to Run

### Prerequisites
You will need a Verilog simulator such as:
* **Icarus Verilog** (Open source)
* **ModelSim / Questasim**
* **Vivado**
* **Verilator**

### Running with Icarus Verilog
1.  Clone the repository:
    ```bash
    git clone [https://github.com/your-username/riscv-single-cycle.git](https://github.com/your-username/riscv-single-cycle.git)
    cd riscv-single-cycle
    ```
2.  Compile the design and testbench:
    ```bash
    iverilog -o cpu_sim src/*.v tb/cpu_tb.v
    ```
3.  Run the simulation:
    ```bash
    vvp cpu_sim
    ```
4.  (Optional) View waveforms in GTKWave:
    ```bash
    gtkwave waveforms.vcd
    ```

---

## 🔮 Future Enhancements
* [ ] Add support for Branch instructions (`BEQ`, `BNE`).
* [ ] Implement Upper Immediate instructions (`LUI`, `AUIPC`).
* [ ] Add visual timing diagrams to documentation.

## 👥 Authors
**GIK Institute Students**
* Department of Computer Engineering

---
