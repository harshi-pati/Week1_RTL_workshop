# Day 1: Introduction to Verilog RTL Design & Synthesis  
Welcome to **Day 1** of the RTL Workshop!  
Today, we’ll explore Verilog, open-source simulation with **Icarus Verilog (iverilog)**, basics of synthesis with **Yosys**, and understand how cell libraries affect design.

---

## Table of Contents

1. [Simulator, Design & Testbench](#1-simulator-design--testbench)  
2. [RTL → Synthesis & Libraries](#2-rtl--synthesis--libraries)  
3. [Fast vs Slow Cells](#3-fast-vs-slow-cells)  
4. [iverilog Simulation Flow](#4-iverilog-simulation-flow)  
5. [2:1 Multiplexer Example](#5-21-multiplexer-example)
6.  [Verilog Code](#6-verilog-code)
7. [Yosys Synthesis Flow](#7-yosys-synthesis-flow)  
8. [Summary](#8-summary)

---

## 1. Simulator, Design & Testbench

### Simulator
* Checks RTL design functionality using *testbench + input vectors*.  
* Event-driven: changes in input → recomputes output.  
* Tool used: **iverilog**.

### Design
* RTL description (Verilog HDL).  
* Implements the required *logic functionality*.  

### Testbench
* Provides stimulus (test vectors) to the design.  
* Checks whether the design behaves as expected.  

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/23f569bf-ad9f-49e8-aa9f-ae48516fdf4c" />

---

## 2. RTL → Synthesis & Libraries

* **Synthesizer** converts RTL → *gate-level netlist*.  
  - Tool: **Yosys**  
  - Inputs: RTL + .lib file  
  - Output: *Netlist* (structural description).  

### .lib (Library File)
* Collection of different *flavours of gates*.  
* Example: AND gate (2-input, 3-input, 4-input…).  
* Provides multiple *timing and drive options*.  

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/0458f6fb-95a0-4522-a341-30ac525ae812" />

---

## 3. Fast vs Slow Cells
* Load in digital circuits = **capacitance**.  
* Faster charging/discharging → lower delay.  

🔹 **Faster cells**  
* Wider transistors → high drive current.  
* Pros: low delay.  
* Cons: more *area + power*.  

🔹 **Slower cells**  
* Narrower transistors.  
* Pros: less area + power.  
* Cons: higher delay.  

👉 Trade-off: faster cells ≠ free, they cost area and power.  

### Selection of Cells
* Synthesizer must choose *optimum flavours*.  

➡ If more **fast cells** used:  
* High power & area.  
* Risk of *hold time violations*.  

➡ If more **slow cells** used:  
* Circuit may be sluggish.  
* May fail timing requirements.  

👉 Designer provides **constraints** to guide the synthesizer.  

---

## 4. iverilog Simulation Flow

**iverilog** is an open-source simulator for Verilog.  

**Flow:**  
* Design + Testbench → iverilog → `.vcd` file → GTKWave.  

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/0458f6fb-95a0-4522-a341-30ac525ae812" />

---

## 5. 2:1 Multiplexer Example

Clone the repo:  
```sh
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
````

Install tools:

```sh
sudo apt install iverilog
sudo apt install gtkwave
```

Compile & simulate:

```sh
iverilog good_mux.v tb_good_mux.v
./a.out
gtkwave tb_good_mux.vcd                  # to view output wavwform
```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/739cabfb-71f3-4ee4-9b62-c37eaaa58601" />

---

## 6. Verilog Code

```sh
gvim tb_good_mux.v -o good_mux.v          # to open the verilog program 

```
<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/93a2b6b7-ff7c-45e7-8492-4f896ffa78a6" />

* Inputs: `i0`, `i1`, `sel`
* Output: `y`
* Logic: if `sel=1` → `y=i1`; else → `y=i0`.

---

## 7. Yosys Synthesis Flow

**Yosys** = open-source synthesis tool.

* Converts HDL → gate-level netlist.
* Features:

  * Synthesis
  * Optimization
  * Technology mapping
  * Verification

### Steps

1. Start yosys:

   ```sh
   yosys
   ```
2. Read liberty library:

   ```sh
   read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
3. Read design:

   ```sh
   read_verilog good_mux.v
   ```
4. Synthesize:

   ```sh
   synth -top good_mux
   ```
5. Technology mapping:

   ```sh
   abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
6. View netlist:

   ```sh
   show
   ```

<img width="1280" height="800" alt="Image" src="https://github.com/user-attachments/assets/d3c61a3c-6349-452f-8fdf-abf641c5c0e3" />

---

## 8. Summary

* Learned simulators, design, and testbenches.
* Simulated a 2:1 mux with **iverilog** & GTKWave.
* Analyzed Verilog mux code.
* Explored Yosys & synthesis flow.
* Understood why libraries have fast/slow cells and trade-offs in cell selection.

---

