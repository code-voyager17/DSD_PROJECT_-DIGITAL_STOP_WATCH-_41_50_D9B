# Smart Digital Stopwatch System (Phase 1)

## Problem Statement
The objective of this project is to design and implement a smart digital stopwatch capable of accurate time tracking up to 59:59 (MM:SS) with advanced Start, Stop, Resume, and Lap/Split functionality. The complete system utilizes a hybrid hardware-software architecture: a sequential logic circuit built on a General-Purpose PCB (GPP) for signal conditioning and timekeeping, paired with a Finite State Machine (FSM) controller implemented on an FPGA using Verilog HDL for intelligent state management.

<img width="1672" height="941" alt="Q18-Smart Digital Stopwatch System Diagram" src="https://github.com/user-attachments/assets/8358a023-8c93-4a0d-ae8c-1923568b39b8" />

## Phase 1 Objective
This repository covers Phase 1: the complete logic design and Logisim simulation of the sequential hardware. This stage validates the component-level timing, memory latching, and GPIO interfacing logic before physical soldering on the GPP and before proceeding to Phase 2 (the Verilog FSM integration).

## Circuit Architecture & Subcircuit Roles
The system is divided into modular subcircuits that handle specific logic tasks before routing control signals (RUN_EN, PAUSE_EN, LAP_FLAG, RESET_SYNC) to the future FPGA controller.

* **Main System Circuit:** Orchestrates user inputs, time counting, lap memory, and multiplexed display output. It acts as the primary sequential processing block meant for the GPP.

<img width="16424" height="9328" alt="main_digital_circuit" src="https://github.com/user-attachments/assets/5f3c8505-3545-401a-8671-ae8339b456f5" />


* **Edge Detector (74LS74 Dual D Flip-Flops):** Provides essential signal conditioning. It debounces raw user inputs (Start/Stop, Lap/Split, Reset) and generates clean single-clock pulses for reliable state transitions.

<img width="5736" height="2800" alt="edge_detector" src="https://github.com/user-attachments/assets/2b80b478-3b49-463d-8ac9-32a55e90b904" />


* **BCD Counter (74LS90):** Cascaded decades handle the core timekeeping, counting seconds (0-9, 0-5) and minutes (0-9, 0-5) synchronously from a 1 Hz clock.

<img width="8008" height="5568" alt="ic_74ls90" src="https://github.com/user-attachments/assets/769132d4-94ca-44ff-9e4c-a8e99a87e0da" />


* **4-Bit Memory Cell (74LS74):** Dedicated register blocks that capture and hold the active BCD count the exact moment the Lap/Split button is pressed, allowing the main 74LS90 counters to continue running in the background.

<img width="6480" height="3024" alt="d_ff_memory_cell" src="https://github.com/user-attachments/assets/36deada1-f115-4f7d-9bf9-c583b2d84226" />


* **Mode Selection Multiplexer (74LS157):** A Quad 2:1 MUX that acts as a data switch. Controlled by the FSM state, it routes either the live time (from the counters) or the frozen lap time (from the memory cells) to the display pipeline.

<img width="5224" height="5888" alt="ic_74ls157" src="https://github.com/user-attachments/assets/e436e060-f8a6-455f-af6f-f4b5ce5c6bf6" />


* **Display Decoder (74LS47):** Converts the final 4-bit BCD signals into standard 7-segment logic to drive the four visual display digits (MM:SS).

<img width="10712" height="9928" alt="ic_74ls47" src="https://github.com/user-attachments/assets/7e01641e-9224-4c72-90ce-582a3134a352" />


*  **74LS74 Dual D Flip-Flops:** Used in memory cell for storing snapshot and used in building the edge detector to generate output pulses for FPGA FSM.

<img width="4920" height="3128" alt="ic_74ls74" src="https://github.com/user-attachments/assets/7408bc08-4b08-46dd-bca8-e4284485060b" />


## Simulation Steps
1. Download and install Logisim, then open the main `.circ` file.
2. Navigate to **Simulate** in the top menu and ensure **Ticks Enabled** is checked.
3. Set the Tick Frequency to **2 Hz** to simulate real-time seconds. (Note: In Logisim, Clock Frequency = 0.5 * Tick Frequency, for faster simulation, higher Tick Frequency can be chosen)
4. Use the **Poke** tool to toggle the `START/STOP` button and verify the counter begins incrementing.
5. While running, toggle the `LAP/SPLIT` button to test the 74LS74 memory cell latching and verify the 74LS157 MUX successfully switches the display to the frozen time by toggling the `SHOW LAP/SPLIT` button.
6. Toggle the `RESET` button to verify all counters clear to 00:00.

## Author
**Tanav Patil**  
  Roll No. 50, D9B EXTC
## Co-Author
**Manas Narkar**
  Roll No. 41, D9B EXTC
